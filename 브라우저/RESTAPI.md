# REST API

> 출처 Modern JavaScript Deep Dive를 보고 정리한 내용입니다.

REST란 HTTP를 기반으로 클라이언트가 서버의 리소스의 접근하는 방식을 규정한 아키텍처고, REST API는 REST를 기반으로 서비스 API를 구현한 것을 의미한다.

REST API는 자원,행위,표현의 3가지 요소로 구성된다. REST는 자체 표현 구조로 구성되어 REST API만으로 HTTP 요청의 내용을 이해할 수 있다.

| 구성 요소 | 내용                           | 표현 방법        |
| --------- | ------------------------------ | ---------------- |
| 자원      | 자원                           | URI(엔드포인트)  |
| 행위      | 자원에 대한 행위               | HTTP 요청 메소드 |
| 표현      | 자원에 대한 행위의 구체적 내용 | 페이로드         |

1. URI는 리소스를 표현해야 한다.

아래 예시처럼 URI는 어떤 리소스를 다루는지에 대해서만 집중하고 get, show같은 행위에 대한 표현이 들어가선 안 된다.

```
#bad
GET /getTodos/1
GET /todos/show/1

#good
GET todos/1

```

2. 리소스에 대한 행위는 HTTP 요청 메소드가 표현한다.

| HTTP 요청 메소드 | 종류           | 목적                  | 페이로드 |
| ---------------- | -------------- | --------------------- | -------- |
| GET              | index/retrieve | 모든/특정 리소스 취득 | x        |
| POST             | create         | 리소스 생성           | o        |
| PUT              | replace        | 리소스의 전체 교체    | o        |
| PATCH            | modify         | 리소스의 일부 수정    | o        |
| DELETE           | delete         | 모든/특정 리소스 삭제 | x        |

```
#bad
GET /todos/delete/1

#good
DELETE todos/1

```
