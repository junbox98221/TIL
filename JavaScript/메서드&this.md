# 메서드와 this

> 출처 [모던 JavaScript 튜토리얼](https://ko.javascript.info/)을 보고 정리한 내용입니다.

객체의 프로퍼티에 함수를 할당할 수 있는데 할당된 함수는 메서드라고 한다.

```
let user = {
  name: "John",
  age: 30
};

user.sayHi = function() {
  alert("안녕하세요!");
};

user.sayHi(); // 안녕하세요!
```

메서드는 객체 내에 저장된 정보에 접근할 수 있어야 한다. 이럴 때 사용하는 키워드가 **this** 이다.

아래 방식으로 접근한다.

```
let user = {
  name: "John",
  age: 30,

  sayHi() {
    // 'this'는 '현재 객체'를 나타냅니다.
    alert(this.name);
  }

};

user.sayHi(); // John
```

이렇게 this 를 사용하지 않고 외부 변수를 참조해 객체의 정보에 접근할 수 있지만 외부 변수가 변하면 에러가 발생할 수 있다.

아래 예시를 보면 user 변수를 복사해 admin에 할당했다. (얕은 복사, 참조값 할당)

그 이후 user = null; 으로 다른 값으로 덮어썼지만 여전히 admin 변수로 접근이 가능해 가비지컬렉션에 의해 메모리에서 사라지자 않는 상태이다.

그리고 외부변수로 sayHi() 메서드에서 객체 내 정보에 접근해서 에러가 발생하게 된 상황이다.

```
let user = {
  name: "John",
  age: 30,

  sayHi() {
    alert( user.name ); // Error: Cannot read property 'name' of null
  }

};


let admin = user;
user = null; // user를 null로 덮어씁니다.

admin.sayHi(); // sayHi()가 엉뚱한 객체를 참고하면서 에러가 발생했습니다.
```

this의 대상, 값은 런타임에 결정된다.

아래에서 같은 f 라는 메서드를 실행하는데 sayHi함수에서 this.name은 각각 user 와 admin 이다.

```
let user = { name: "John" };
let admin = { name: "Admin" };

function sayHi() {
  alert( this.name );
}

// 별개의 객체에서 동일한 함수를 사용함
user.f = sayHi;
admin.f = sayHi;

// 'this'는 '점(.) 앞의' 객체를 참조하기 때문에
// this 값이 달라짐
user.f(); // John  (this == user)
admin.f(); // Admin  (this == admin)

admin['f'](); // Admin (점과 대괄호는 동일하게 동작함)
```

화살표함수는 일반 함수와 다르게 '고유한' this를 가지지 않는다.

화살표 함수에서 this는 외부 함수에서 this값을 가져온다.

우선 아래 예시를 각각 실행해보자.

```
let user = {
  firstName: "보라",
  sayHi: () => alert(this.firstName)
};

user.sayHi(); // undefined

let user = {
  firstName: "보라",
  sayHi: function(){ alert(this.firstName)}
};

user.sayHi(); // 보라
```

화살표함수로 만든 첫번째 sayHi()는 undefined를 출력하지만 일반 함수형식의 2번째 sayHi는 user 객체의 firstName의 프로퍼티에 접근가능하다.

아래 화살표 함수로 만든 arrow함수는 외부 변수인 user 에 접근한다.

```
let user = {
  firstName: "보라",
  sayHi() {
    let arrow = () => alert(this.firstName);
    arrow();
  }
};

user.sayHi(); // 보라
```
