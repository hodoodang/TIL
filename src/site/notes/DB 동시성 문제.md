---
{"dg-publish":true,"permalink":"/db/","dgHomeLink":true,"dgPassFrontmatter":false}
---


written by: hodoodang
created at: 2022-09-25
tags: #DB, #Concurrency

---

# DB 동시성 문제

동시에 같은 `DB Table row` 를 업데이트 하는 상황을 방어해야 하는 상황을 방어하기 위해 어떻게 개발하실 건지 설명해주세요.
- 데이터베이스의 동시성 문제이며, 업데이트 상황이기 때문에 쓰기 작업으로 볼 수 있다.
- 동시성 문제의 대표적인 해결 방법은 낙관적 동시성 제어 방법과 비관적 동시성 제어 방법이 있다.
- 이때 낙관적 동시성 제어 방법의 경우 같은 데이터를 동시에 수정하지 않을 것이라 가정하여 수행하는 방법이기 때문에 문제 상황에는 어울리지 않는다.
- 비관적 동시성 제어 방법의 경우 문제 상황에 딱 맞는 방법이라고 생각할 수 있지만, 성능이 매우 낮아질 수 있다.
- 그렇다면 생각해 볼 수 있는 다른 방법은 다중버전 동시성 제어(Multiversion Concurrency Control, MVCC) 방법이 있다.
	- MVCC 방법은 데이터를 변경할 때마다 변경 사항을 Undo 영역에 저장해두고, 사용자의 접근 시점에 맞는 데이터베이스의 Snapshot을 읽는다.