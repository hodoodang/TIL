---
{"dg-publish":true,"permalink":"/python/fast-api/background-task/","dgHomeLink":true,"dgPassFrontmatter":false}
---


written by: hodoodang
created at: 2022-08-18
tags: #Backend, #Python, #FastAPI 

---

# 백그라운드 작업(Background Task)
response를 전달한 후 실행할 백그라운드 작업을 만들 수 있습니다.

`Background Task`는 요청 후에 수행되어야 하지만 클라이언트가 작업이 완료되었다는 응답을 기다릴 필요가 없는 작업에 유용합니다.

다음이 이에 해당합니다.
- 작업 수행 후 전송되는 이메일 알림:
	- 이메일 서버에 연결하여 이메일을 보내는 것은 "느린" 경향이 있으므로 즉시 응답(ex. 작업 시작 성공 등)을 반환하고 백그라운드에서 이메일 알림을 보낼 수 있습니다.
- 데이터 처리:
	- 느린 프로세스(ex. 분석, 가공 등)를 거쳐야 하는 파일을 받은 경우, "Accepted"(HTTP 202) 응답을 반환하고 백그라운드에서 처리할 수 있습니다.

## `BackgroundTasks` 사용하기
다음과 같이 `BackgoundTasks`를 추가 및 선언하고, 작업을 추가하여 사용합니다.
```python
from fastapi import BackgroundTasks, FastAPI

app = FastAPI()

def write_notification(email: str, message=""):
    with open("log.txt", mode="w", encoding="utf-8") as email_file:
        content = f"notification for {email}: {message}"
        email_file.write(content)

@app.post("/send-notification/{email}")
async def send_notification(email: str, background_tasks: BackgroundTasks):
    background_tasks.add_task(write_notification, email, message="some notification")
    return {"message": "Notification sent in the background"}
```
