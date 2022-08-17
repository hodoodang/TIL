---
{"dg-publish":true,"permalink":"/python/fast-api/middleware/","dgHomeLink":true,"dgPassFrontmatter":false}
---


written by: hodoodang
created at: 2022-08-17
tags: #Backend, #Python, #FastAPI

---

# 미들웨어(middleware)
`middleware`란 모든 request에 대해 path operation이 수행되기 전에 실행되는 함수를 말합니다. 또한 모든 response에 대해서도 response를 return 하기 전에 실행되는 함수도 middleware라 합니다.

Spring에서는 Intercepter, Filter가, Flask에서는 App Context 중 before_request나 after_request 등의 콜백 함수로 처리하는 등의 여러가지 방법과 동일합니다.

> 만약 `yield`를 사용한다면, 이에 대한 응답은 미들웨어가 모두 실행된 이후에 옵니다.
> 만약 [[Python/FastAPI/백그라운드 작업(Background Task)|백그라운드 작업]]이 있다면, 이는 미들웨어가 모두 실행된 후 수행됩니다.

## BaseHTTPMiddleware
FastAPI에서 제공하는 Middleware 패턴은 `BaseHTTPMiddleware`를 상속한 클래스들로 동작합니다. 이 클래스는 클라이언트로부터 요청이 오면 그 요청을 받은 뒤 dispatch 함수를 호출하는데, 이 dispatch 함수에서 중간 처리를 진행할 수 있습니다.

# Middleware

# Advanced Middleware