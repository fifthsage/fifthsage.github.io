---
title: "express middleware"
tags:
  - middleware
categories: 
  - dev
  - nodejs
  - express
toc: true
---

## 기본

```js
const middleware = (req, res, next) => {
  console.log('middleware')
  next()
})

router.use('path', middleware, (req, res, next) => {
  console.log('route')
})
```

결과

```zsh
middleware
route
```

## 다중 미들웨어

```js
const middleware1 = (req, res, next) => {
  console.log('middleware1')
  next()
})
const middleware2 = (req, res, next) => {
  console.log('middleware2')
  next()
})

// 순서대로 미들웨어 수행
router.use('path', middleware1, middleware2 (req, res, next) => {
  console.log('route')
})
```

결과

```zsh
middleware1
middleware2
route
```
