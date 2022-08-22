---
{"dg-publish":true,"permalink":"/python/fast-api/async-vs-sync-vs-parallel/","dgHomeLink":true,"dgPassFrontmatter":false}
---


written by: hodoodang
created at: 2022-08-22
tags: #Backend, #Python, #FastAPI 

---

# Async vs Sync vs Parallel
비동기와 동기 그리고 병렬을 구현하고, 각 방식에 따른 장단점 또는 특징을 비교 해보도록 하겠습니다.

우선 비동기와 동기, 병렬에 대한 router를 작성하기 전에 각 router를 호출하여 부하를 테스트할 request 함수를 작성합니다.

```python
import requests
from random import randint
from concurrent.futures import ThreadPoolExecutor
```
우선 API를 호출하는 `requests` 와 멀티 스레딩을 통해 API를 동시 호출할 때 필요한 `ThreadPoolExecutor` 를 추가합니다.

```python
def get_urls(url: str, duration: int) -> List[str]:
    return [url + str(randint(1, limit)) for i in range(1, duration + 1)]
```
호출할 API의 주소와 파라미터에 대해 호출할 횟수 만큼 리스트로 리턴하는 함수를 사용합니다. 여기서 1부터 `duration` 으로 전달받은 값까지 랜덤한 값이 파라미터로 작성됩니다. 이는 호출될 API에서 `time.sleep()` 함수에서 사용할 파라미터입니다.

```python
def call_get(url: str):
    return requests.get(url)
```
이제는 실제 API를 호출하는 requests 함수를 작성합니다.

