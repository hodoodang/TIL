---
{"dg-publish":true,"permalink":"/res-tful-api/","dgHomeLink":true,"dgPassFrontmatter":false}
---


written by: hodoodang
created at: 2022-09-04
tags: 

---

# REST API
REST(REpresentational State Transfer) API란 두 컴퓨터 시스템이 인터넷을 통해 정보를 안전하게 교환하기 위해 사용하는 인터페이스입니다. 

또 어떠한 문서를 저장할 때 규약을 갖고 저장하자는 약속이라고 할 수도 있다.

하지만 REST API는 통신 규약(프로토콜)이 아니기 때문에 꼭 지켜져야만 하는 것은 또 아니다. 이것은 통신 규약(프로토콜) 중 하나인 HTTP를 보다 더 제대로 사용하기 위해 HTTP의 저자 중 한 사람인 로이 필딩(Roy Fielding)이 발표한 아키텍처이자 인터페이스이다.

## REST 구성
REST API는 다음 3가지 구성으로 이루어져 있다.  
- **자원(Resource)** - URI
- **행위(Verb)** - Method
- **표현(Representations)**

이는 REST라는 이름이기도 하다.


## 좋은 데이터베이스 설계에서부터 시작한다.
- 엔티티 간의 반복적인 연결이나 사이클 형태의 연결이 이루어지 않도록 한다.
- 엔티티에는 해당 엔티티에 관련된 데이터만 포함하며, 다른 엔티티와 종속적인 관계가 없어야 한다.
- 새로운 디펜던시를 추가할 때, 기존의 테이블을 크게 재작업 하지 않고, 안전하게 추가할 수 있어야 한다.

# Reference
- https://dev.to/khalyomede/design-an-easy-to-use-and-flexible-rest-endpoints-3fia
- https://meetup.toast.com/posts/92
- https://ko.wikipedia.org/wiki/REST
- https://aws.amazon.com/ko/what-is/restful-api/
- https://www.redhat.com/ko/topics/integration/whats-the-difference-between-soap-rest
- https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html
