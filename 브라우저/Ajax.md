# Ajax

> 출처 Modern JavaScript Deep Dive를 보고 정리한 내용입니다.

Ajax 는 자바스크립트를 사용하여 브라우저가 서버에게 비동기 방식으로 데이터를 요청하고, 서버가 응답한 데이터를 수신하여 웹페이지를 동적으로 갱신하는 프로그래밍 방식이다.

Ajax 이전에는 html 태그로 시작해 html 태그로 끝나는 완전한 HTML을 서버로부터 전송받아 웹페이지 전체를 처음부터 다시 렌더링하는 방식으로 동작했는데 이는 다음과 같은 문제점이 존재했다.

1. 이전 웹페이지와 변경이 없어 갱신할 필요가 없는 부분까지도 매번 다시 전송받아 불필요한 데이터 통신이 필요하다.

2. 변경할 필요가 없는 부분까지 처음부터 다시 렌더링한다. 이로 인해 화면 전환이 일어나면 화면이 순간적으로 깜빡이는 현상이 발생한다.

3. 클라이언트와 서버와의 통신이 동기 방식으로 동작하기 때문에 서버로부터 응답이 있을 때까지 다음 처리는 블로킹된다.

## JSON

JSON이란 클라이언트와 서버 간의 HTTP 통신을 위한 텍스트 데이터 포맷이다.

클라이언트가 서버로 객체,배열을 전송하려면 이를 문자열화하는데 이를 직렬화(serializing)이라 하고 JSON.stringify 메서드를 사용한다.

```js
const obj = {
    name: "Lee",
    age: 20,
    alive: true,
};

const json = JSON.stringify(obj);
```

반대로 서버로부터 받은 JSON 포맷의 문자열을 객체 혹은 배열로 반환하기 위해서는 JSON.parse() 메서드를 사용한다.

```js
const obj = {
    name: "Lee",
    age: 20,
    alive: true,
};

const json = JSON.stringify(obj);

const obj2 = JSON.parse(json);
```

## XMLHttpRequest

브라우저는 주소창이나 HTML의 form 태그, a태그를 통해 HTTP 요청 전송 기능을 기본 제공한다.

자바스크립트를 사용하여 HTTP 요청을 전송하려면 XMLHttpRequest 객체를 사용한다.

Web API인 XMLHttpRequest 객체는 HTTP 요청 전송과 HTTP 응답 수신을 위한 다양한 메서드와 프로퍼티를 제공한다.
