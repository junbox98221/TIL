# new연산자와 생성자 함수

> 출처 [모던 JavaScript 튜토리얼](https://ko.javascript.info/)을 보고 정리한 내용입니다.

1. 생성자 함수

유사한 객체를 여러 개 만들어야 할 때 new연산자를 사용하는데 아래 두 관례를 따른다.

-   함수 이름의 첫 글자는 대문자
-   반드시 'new' 연산자를 붙여 실행

```
function User(name) {
  this.name = name;
  this.isAdmin = false;
}

let user = new User("보라");

alert(user.name); // 보라
alert(user.isAdmin); // false
```

new User(...)를 써서 함수를 실행하면 아래와 같은 알고리즘이 동작한다.

1. 빈 객체를 만들어 this에 할당한다.
2. 함수 본문을 실행하고 this에 새로운 프로퍼티를 추가해 this를 수정한다.
3. this 를 반환한다.

아래 코드를 보면 이해가 쉽다.

```
function User(name) {
  // this = {};  (빈 객체가 암시적으로 만들어짐)

  // 새로운 프로퍼티를 this에 추가함
  this.name = name;
  this.isAdmin = false;

  // return this;  (this가 암시적으로 반환됨)
}

user는 아래 코드와 동일해진다.
let user = {
  name: "보라",
  isAdmin: false
};
```

생성자는 객체 리터럴 보다 쉽게 재사용할 수 있는 객체 생성 코드를 구현한다.
