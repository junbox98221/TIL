# es6

> 출처 Modern JavaScript Deep Dive를 보고 정리한 내용입니다.

es6이전에는 함수를 일반적인 함수로 호출할 수 있고 생성자 함수로서 호출할 수 있고 객체에 바인딩되어 메서드로서 호출할 수 있었다.

즉 모든 함수가 callable이면서 constructor이었다.(호출할 수 있는 함수 객체를 callable, 인스턴스를 생성할 수 있는 함수 객체를 constructor, 인스턴스를 생성할 수 없는 함수를 non-constructor라고 한다.)

```js

Var foo = function(){
  return 1;
}

foo(); //일반적인 함수

new foo();// 생성자 함수

var obj = {foo:foo}; //객체 메서드
obj.foo(); // 1
```

이것은 편해 보이지만 실수를 유발할수도 있고 성능면에서도 손해이다.

객체에 바인딩된 함수를 생성자 함수로 사용하지 않는 경우 불필요한 prototype 프로퍼티를 갖고 프로토타입 객체도 생성된다.

이러한 부분 때문에 es6이전고 ㅏ이후의 메서드와 화살표 함수는 명확한 차이가 있다.

## 메서드

```js
const obj = {
    x: 1,
    foo() {
        // foo는 메서드 축약 표현으로 생성된 메서드이다.
        return this.x;
    },
    bar: function () {
        //bar는 메서드가 아닌 일반 함수이다.
        return this.x;
    },
};

console.log(obj.foo()); //1 호출할 땐 둘 모두 같다.
console.log(obj.bar()); //1

new obj.foo(); //TypeError obj.foo is not constructor
new obj.bar(); // {}
```

es6 메서드는 자신을 바인딩한 객체를 가리키는 내부 슬롯 [[HomeObject]]를 갖는다.

super참조는 내부 슬롯 [[HomeObject]]를 사용하여 수퍼클래스의 메서드를 참조하므로 내부 슬롯 [[HomeObject]]를 갖는 es6메서드는 super 키워드를 사용할 수 있다.

```js
const base = {
    name: "Lee",
    sayHi() {
        return `Hi! ${this.name}`;
    },
};

const derived = {
    __proto__: base,
    sayHi() {
        return `${super.sayHi()}. how are you?`;
    },
    sayHi2: function () {
        return `${super.sayHi()}. how are you?`;
    },
};
console.log(derived.sayHi()); //Hi! Lee. how are you?
console.log(derived.sayHi2()); // SyntaxError: 'super' keyword unexpected here
```

es6메서드가 아닌 일반함수는 내부 슬롯 [[HomeObject]]를 갖지 못한다.

es6 메서드는 본연의 기능 super를 추가하고 의미적으로 맞지 않는 기능 constructor를 제거했다.

따라서 객체 내 메서드를 정의할 때 프로퍼티 값으로 익명함수 표현식을 할당하는 이전의 방식은 사용하지 말자.

```js
let a = () => {
    return { 1: 2 };
};
let a = () => ({ 1: 2 });
```

## 화살표 함수

화살표 함수는 인스턴스를 생성할 수 없는 non-constructor이다.

```js
const Foo = () => {};

new Foo(); // TypeError Foo is not a constructor
```

화살표 함수는 함수 자체의 this, arguments, super, new.target 바인딩을 갖지 않는다.

만약 화살표 함수 내부에서 this, arguments, super, new.target를 참조하면 스코프 체인을 통해 상위 스코프의 this, arguments, super, new.target를 참조한다.
