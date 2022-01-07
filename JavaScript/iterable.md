# iterable

> 출처 [모던 JavaScript 튜토리얼](https://ko.javascript.info/)을 보고 정리한 내용입니다.

**iterable 객체**

객체에 Symbol.iterator라는 메서드가 있는 객체

-   문자열
-   배열
-   set
-   map 이 여기에 해당된다.

**유사 배열**

이터러블과 비슷하지만 Sysbol.iterator가 있지 않고 인덱스와 length프로퍼티가 있어 배열처럼 보이는 객체이다.

이터러블이 아니므로 for of 를 사용할 수 없다.

```
let arrayLike = { // 인덱스와 length프로퍼티가 있음 => 유사 배열
  0: "Hello",
  1: "World",
  length: 2
};
```

**Array.from**

범용 메서드 Array.from은 이터러블이나 유사 배열만을 받아 진짜 Array를 만들어준다.
