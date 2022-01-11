# nullish

> 출처 [모던 JavaScript 튜토리얼](https://ko.javascript.info/)을 보고 정리한 내용입니다.

nullish는 null 과 undefined가 포함된 변수를 효과적으로 구별하기 위해 사용된다.

```
x = a ?? b;
x = (a !== null && a !== undefined) ? a : b;
```

두 코드는 같은 동작을 한다. a가 null 이거나 undefined가 아니라면 x에 a가 대입되고 그 외에는 b가 대입된다.
