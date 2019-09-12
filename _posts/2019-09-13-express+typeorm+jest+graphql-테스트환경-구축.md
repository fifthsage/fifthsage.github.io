---
title: "express+typeorm+jest+graphql 테스트환경 구축"
tags:
  - 테스트환경
  - 단위테스트
  - test
  - jest
  - typescript
  - express
  - graphql
categories: 
  - 테스트
  - nodejs
  - express
  - jest
toc: true
---

## 준비물
- typeorm
- jest
- express
- graphql
- supertest

## 폴더 구조
.
+-- __tests__
|   +-- Feature
|       +-- api.ts
|       +-- login.test.ts
|   +-- Unit
|       +-- database.ts
|       +-- loginService.test.ts
|   +-- setup.ts
+-- databaseConn.ts
+-- server.ts
+-- ormconfig.ts
+-- package.json
+-- ...

## root

### package.json
jest관련 설정중 setupFilesAfterEnv에 setup.ts를 추가합니다. test시 전역 설정을 할 수 있습니다.
```json
...
"jest": {
    "testPathIgnorePatterns": [
      "<rootDir>/node_modules/"
    ],
    "transform": {
      "^.+\\.(ts|tsx)$": "ts-jest"
    },
    "globals": {
      "ts-jest": {
        "tsConfig": "tsconfig.json"
      }
    },
    "testRegex": "\\.(test|spec)\\.((js|ts))$",
    "setupFilesAfterEnv": [
      "<rootDir>/__tests__/setup.ts"
    ],
    "cacheDirectory": ".jest/cache"
  },
...
```

### ormconfig.ts
test 커넥션을 설정합니다. synchronize와 dropSchema에 유의하세요.
```js
import { ConnectionOptions } from "typeorm";

const connectionOptions: ConnectionOptions[] = [
  {
    name: "default",
    type: "mysql",
    database: 'db',
    synchronize: false,
    logging: true,
    entities: [__dirname + "/src/entities/**/*.ts"],
    subscribers: [__dirname + "/src/subscribers/**/*.ts"],
    migrations: [__dirname + "/databases/migrations/**/*.ts"],
    migrationsTableName: "migrations",
    cli: {
      entitiesDir: "src/entities",
      subscribersDir: "src/subscribers",
      migrationsDir: "databases/migrations"
    },
    host: 'host',
    port: 'port',
    username: 'user',
    password: 'password'
  },
  {
    name: "test",
    type: "mysql",
    database: 'db_test',
    synchronize: true,
    dropSchema: true,
    logging: false,
    entities: [__dirname + "/src/entities/**/*.ts"],
    subscribers: [__dirname + "/src/subscribers/**/*.ts"],
    migrations: [__dirname + "/databases/migrations/**/*.ts"],
    host: 'host',
    port: 'port',
    username: 'user',
    password: 'password'
  }
];

export = connectionOptions;
```

### databaseConn.ts
환경변수에 따른 데이터베이스 커넥션을 설정합니다.
```js
import { createConnection, getConnectionOptions, getConnection } from "typeorm";

export default async () => {
  let name = "default";
  if (process.env.NODE_ENV === "test") {
    name = process.env.NODE_ENV;
  }

  const connectionOptions = await getConnectionOptions(name);
  await createConnection({ ...connectionOptions, name: "default" });
};

export const closeDatabaseConn = async () => {
  getConnection().close();
};
```

### server.ts
일반적인 express server입니다.
```js
import express from "express";
import apollo from "./apollo";
import databaseConn from "./databaseConn";

const app = express();

const listen = async () => {
  await databaseConn();

  app.listen(common.port, () => {
    console.log(`🚀 Server ready`);
  });
};

apollo.applyMiddleware({
  app,
  path: "/api"
});

export default {
  getApp: () => app,
  listen
};
```

## __tests__

### __tests__/setup.ts
test환경을 설정합니다.
```js
process.env.NODE_ENV = "test";

beforeAll(async () => {});

afterAll(async () => {});

```

### Unit/database.ts
유닛 테스트에서 쓰일 데이터베이스 연결 모듈입니다.
```js
import { closeDatabaseConn } from "../../src/helpers/databaseConn";
import databaseConn from "../../src/helpers/databaseConn";

beforeAll(async () => {
  await databaseConn();
});

afterAll(async () => {
  await closeDatabaseConn();
});
```

### Unit/LoginService.test.ts
데이터베이스를 사용하는 LoginService를 위해 database를 import시켜줍니다.
```js
import LoginService from "...";
import User from "...";
import "./database";

describe("Services / Login", () => {
  it("login", async () => {
    const loginService = new LoginService();

    expect(await loginService.login()).toBeInstanceOf(User);
  });
});
```

### Feature/api.ts
Feature 테스트에서 쓰일 api 모듈입니다.
```js
import request from "supertest";
import server from "../../server";

const app = server.getApp();

export default request(app)
  .post("/api")
  .set("Content-Type", "application/json")
  .set("Accept", "application/json");
```

### Feature/Login.test.ts
login mutation에 대해 테스트를 합니다. response를 반환 받아 다른 테스트를 진행 할 수 있습니다.
```js
import api from "./api";

describe("Api / Login", () => {
  it("Login", async () => {
    await api
      .send({
        query: `mutation {
          login(
            userId: "id"
            password: "password"
          ) {
            user {
              id
              name
            }
          }
        }`
      })
      .expect(200);
  });
});
```
