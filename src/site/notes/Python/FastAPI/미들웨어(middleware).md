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

dispatch 함수에서는 BaseHTTPMiddleware가 클라이언트로부터 받은 요청과 EndPoint 함수를 파라미터를 제공하여 넘겨줍니다.

이를 활용하면 API 요청시 걸리는 시간을 Header에 포함해서 클라이언트로 넘겨주는 미들웨어를 만들 수 있습니다.

```python
import time  
from fastapi import Request  
from starlette.middleware.base import BaseHTTPMiddleware, RequestResponseEndpoint  
from starlette.responses import Response  
  
  
class TimeHeaderMiddleware(BaseHTTPMiddleware):  
    async def dispatch(self, request: Request, call_next: RequestResponseEndpoint) -> Response:  
        start_time = time.time()  
        res = await call_next(request)  
        process_time = time.time() - start_time  
        res.headers['X-Process-Time'] = str(process_time)  
        return res
```

Python에서 제공하는 time 함수를 통해 미들웨어로부터 요청 값을 받아 온 시간부터 call_next를 통해 EndPoint으로 요청 값을 넘겨주고, 응답 값을 받아 온 시간까지 측정합니다.

이 시간을 call_next에서 받은 응답 값 response의 header 값에 넣어주면 클라이언트에게 이 값과 함께 전송되게 됩니다.

## Require JSON
클라이언트가 PUT, PATCH, POST 메소드를 호출했을 때 JSON 형태의 데이터만을 받도록 할 때도 미들웨어를 이용하여 간단히 처리할 수 있습니다.

```python
from fastapi import Request  
from fastapi.responses import PlainTextResponse, ORJSONResponse  
from starlette.middleware.base import BaseHTTPMiddleware, RequestResponseEndpoint  
from starlette.responses import Response  
  
  
class RequireJSON(BaseHTTPMiddleware):  
    async def dispatch(  
        self, request: Request, call_next: RequestResponseEndpoint  
    ) -> Response:  
        if request.method in ('POST', 'PUT', 'PATCH'):  
            if request.headers.get('content-type') != 'application/json':  
                return PlainTextResponse(status_code=415)  
        return await call_next(request)
```
request의 정보 중 메소드를 확인하고, content-type이 application/json인지 확인합니다. 만약 application/json이 아니라면 415 상태를 반환해주고, 맞다면 요청을 EndPoint에 넘겨주도록 합니다.

# Middleware

# Advanced Middleware

|    Name    | Type   | Default | Description                                                    |
|:----------:|:------ |:------- |:-------------------------------------------------------------- |
| returnType | String | 분리형  | 반환 형태를 지정("sentence" 이외의 값에 대해 모두 분리형 반환) |

|   Name   | Type         | Description                                |
|:--------:|:------------ |:------------------------------------------ |
|   text   | String       | 형태소 분석할 문장                         |
| morpheme | List[String] | 결과로 받을 형태소(빈 값인 경우 모두 출력) |
