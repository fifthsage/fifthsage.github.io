---
title: "express에서 받은 파일 업로드 laravel에 upload하기"
tags:
  - axios
  - multer
  - FormData
categories: 
  - dev
  - nodejs
  - express
toc: true
---

## 문제

express에서 업로드 받은 파일을 request body가 파싱을 하지 못하고, 파싱 후에는 폼 데이터로 전송이 안됨.

## 해결하기 전에 알아야할 것들

기본적으로 클라이언트(웹)에서도 파일 핸들링을 위해 FormData를 쓰는것처럼 express에서도 FormData의 이용이 가능하다.

`npm i -S form-data`

또한 파일 업로드를 제어하기 위해 multer를 이용한다.

`npm i -S multer`

## 해결

router.js
```nodejs
import express from 'express'
import axios from 'axios'
const router = express.Router()

const upload = multer({ dest: 'tmps/' })  // dest경로로 업로드 됨
const FormData = require('form-data')
const fs = require('fs')

router.post('/upload', upload.single('file'), async (req, res, next) => {
  try {
    let form = new FormData()

    form.append('file', fs.createReadStream(req.file.path)) // dest로 지정된 위치에서 기본 지정 파일명으로 path에 저장됨

    const { data } = await axios.post('{api server}/upload', form, {
      headers: form.getHeaders()
    })

    res.json(data)
  } catch (err) {
    next(err)
  }
})
```
