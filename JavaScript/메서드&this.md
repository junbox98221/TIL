# 메서드와 this

> 출처 [모던 JavaScript 튜토리얼](https://ko.javascript.info/),Modern JavaScript DeepDive를 보고 정리한 내용입니다.

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

객체 리터럴로 생성한 경우 이렇게 this 를 사용하지 않고 외부 변수를 참조해 객체의 정보에 접근할 수 있지만 외부 변수가 변하면 에러가 발생할 수 있다.

아래 예시를 보면 user 변수를 복사해 admin에 할당했다. (얕은 복사, 참조값 할당)

그 이후 user = null; 으로 다른 값으로 덮어썼지만 여전히 admin 변수로 접근이 가능해 가비지컬렉션에 의해 메모리에서 사라지자 않는 상태이다.

그리고 외부변수로 sayHi() 메서드에서 객체 내 정보에 접근해서 에러가 발생하게 된 상황이다.

```js
let user = {
    name: "John",
    age: 30,

    sayHi() {
        alert(user.name); // Error: Cannot read property 'name' of null
        //에러를 발생시키는 방식
        alert(this.name); //this 사용 권장
    },
};

let admin = user;
user = null; // user를 null로 덮어씁니다.

admin.sayHi(); // sayHi()가 엉뚱한 객체를 참고하면서 에러가 발생했습니다.
```

this 바인딩은 함수 호출 방식, 함수가 어떻게 호출되었는지에 따라 동적으로 결정된다.

아래에서 같은 f 라는 메서드를 실행하는데 sayHi함수에서 this.name은 각각 user 와 admin 이다.

```js
let user = { name: "John" };
let admin = { name: "Admin" };

function sayHi() {
    alert(this.name);
}

// 서로 다른 객체에 메서드를 추가
user.f = sayHi;
admin.f = sayHi;

// 메서드 내부에서 this는 메서드를 호출한 객체를 가르킨다.
user.f(); // John  (this == user)
admin.f(); // Admin  (this == admin)
```

아래는 프로토타입과 관련된 this 바인딩의 예시이다.

```js
function Person(name) {
    this.name = name;
}

Person.prototype.getName = function () {
    return this.name;
};

const me = new Person("Lee");
console.log(me.getName()); // getName 메서드를 호출한 객체, 인스턴스는 me 이다. 따라서 name 은 Lee 이다.

Person.prototype.name = "Kim";
console.log(Person.prototype.getName()); //Person.prototype 또한 하나의 객체로 여기서 getName는 Kim을 호출한다.
```

생성자 함수 내부의 this는 생성자 함수가 미래에 생성할 인스턴스를 가르킨다.

```js
function Circle(radius) {
    this.radius = radius;
    this.getDiameter = function () {
        return 2 * this.radius;
    };
}

const circle1 = new Circle(5);

const circle2 = new Circle(10);

console.log(circle1.getDiamter()); //10
console.log(circle2.getDiamter()); //20
```
