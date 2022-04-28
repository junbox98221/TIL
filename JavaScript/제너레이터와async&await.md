# 제너레이터와 async & await

> 출처 Modern JavaScript Deep Dive 보고 정리한 내용입니다.

es6에서 도입된 제너레이터는 코드 블록의 실행을 일시 중지했다가 필요한 시점에 재개할 수 있는 특수한 함수다. 제너레이터와 일반 함수의 차이는 다음과 같다.

1. 제너레이터 함수는 함수 호출자에게 함수 실행의제어권을 양도할 수 있다.

2. 제너레이터 함수는 함수 호출자와 함수 상태를 주고받을 수 있다.

3. 제너레이터 함수를 호출하면 제너레이터 객체를 반환한다.

제너레이터 함수를 정의하는 방식은 다음과 같다.

```js
// 제너레이터 함수 선언문
function* getDecFunc() {
    yield 1;
}

// 제너레이터 함수 표현식
const getExpFunc = function* () {
    yield 1;
};
//제너레이터 메서드
const obj = {
    *getObjMethod() {
        yield 1;
    },
};
//제너레이터 클래스 메서드
class MyClass {
    *genClsMethod() {
        yield 1;
    }
}
```

### 제너레이터 객체

제너레이터 함수를 호출하면 일반 함수처럼 코드 블록을 실행하는 것이 아니라 제너레이터 객체를 생성해 반환한다.

제너레이터 함수가 반환한 제너레이터 객체는 이터러블이면서 이터레이터이다.

1. Symbol.iterator 메서드를 상속받은 이터러블이면서
2. value,done 프로퍼티를 갖는 이터레이터 리절트 객체를 반환하는 next 메서드를 소유하는 이터레이터이다.

```js
function* genFunc() {
    yield 1;
    yield 2;
    yield 3;
}

const generator = genFunc();

console.log(Symbol.iterator in generator); //true
console.log("next" in generator); //true
```

제너레이터 객체는 next 메서드를 갖는 이터레이터이지만 이터레이터에는 없는 return, throw 메서드를 갖는다.
next 메서드를 호출한 경우

-   제너레이터 함수의 yield 표현식까지 코드 블록을 실행하고 yield된 값을 value 프로퍼티 값으로, false를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환한다.

return 메서드를 호출한 경우

-   인수로 전달받은 값을 value 프로퍼티 값으로, true를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환한다.

throw 메서드를 호출한 경우

-   인수로 전달받은 에러를 발생시키고 undefined를 value 프로퍼티 값으로, true를 done 프로퍼티 값으로 갖는 이터레이터 리절트 객체를 반환한다.

```js
function* genFunc() {
    try {
        yield 1;
        yield 2;
        yield 3;
    } catch (e) {
        console.log(e);
    }
}

const generator = genFunc();
console.log(generator.next()); // {value:1,done:false}
console.log(generator.return("end")); // {value:'end',done:true}
console.log(generator.throw("error")); // {value:undefined,done:true}
```

제너레이터는 yield 키워드와 next 메서드를 통해 실행을 일시 중지했다가 필요한 시점에 재개할 수 있다.

generator.next() -> yield -> generator.next() -> yield ... -> generator.next() -> return

위의 과정을 반복하다 끝까지 실행되면 next 메서드가 반환하는 이터레이터 리절트 객체의 value 프로퍼티에는 제너레이터 함수의 반환값이 할당되고 done 프로퍼티에는 제너레이터 함수가 끝까지 실행되었는지를 나타내는 불리언 값이 할당된다.

제너레이터 객체의 next메서드에 전달한 인수는 제너레이터 함수의 yield 표현식을 할당받는 변수에 할당한다. 아래 예제를 보자.

```js
function* genFunc() {
    const x = yield 1;
    // 첫 next메서드가 호출되는 시점에 yield 표현식이 실행되고 일시 중지된다.
    // 이때 yield된 값 1은 next 메서드 반환 객체의 value 프로퍼티에 할당되고 x 변수에는 아무것도 할당되지 않았다.
    // x변수는 두 번째 호출될 때 결정된다.
    const y = yield x + 10;
    // 두 번째 next 메서드를 호출할 때 전달한 인수 10은 첫 번쨰 yield 표현식을 할당받는 x 변수에 할당된다.
    // 2번째 next 메서드 반환 객체의 value 프로퍼티는 20이 된다.
    return x + y;
    // 3번째 next 메서드를 실행하면 전달받은 값 30은 y에 할당되어 최종 반환값은 10 + 30 이다.
    // 단, 일반적으로 제너레이터의 반환값은 의미가 없다.
    // 따라서 제너레이터에서는 값을 반환할 필요가 없고 return을 종료의 의미로만 사용해야 한다.
}

const generator = genFunc();

let res = generator.next();
console.log(res); //  {value: 1, done: false}

res = generator.next(10);
console.log(res); //  {value: 20, done: false}
res = generator.next(30);
console.log(res); //  {value: 40, done: true}
```

이처럼 제너레이터 함수는 next 메서드와 yield 표현식을 통해 함수 호출자와 함수의 상태를 주고받을 수 있다.

함수 호출자는 next 메서드를 통해 yield 표현식까지 함수를 실행시켜 제너레이터 객체가 관리하는 상태를 꺼내올 수 있고, next 메서드에 인수를 전달하여 제너레이터 객체에 상태를 밀어넣을 수 있다.

---

## async&await

제너레이터를 이용해서 비동기 처리를 할 순 있지만 코드가 장황해지고 가독성이 나쁘다.

ES8에서는 간단하고 가독성 좋게 비동기 처리를 동기 처리처럼 동작하도록 구현할 수 있는 asnyc,await이 도입되었다.

async/await은 프로미스를 기반으로 동작한다.

await키워드는 프로미스가 settled 상태(비동기 처리가 수행된 상태)가 될 때까지 대기하다가 settled 상태가 되면 프로미스가 resolved한 처리 결과를 반환한다.

await 키워드는 반드시 프로미스 앞에서 사용해야 한다.

아래처럼 모든 프로미스에 await 키워드를 사용하는 것은 주의해야 한다. 위 예제는 foo 함수가 종료될 때까지 9초가 소요된다.

```js
async function foo() {
    const a = await new Promise((resolve) =>
        setTimeout(() => resolve(1), 3000)
    );
    const b = await new Promise((resolve) =>
        setTimeout(() => resolve(2), 3000)
    );
    const c = await new Promise((resolve) =>
        setTimeout(() => resolve(3), 3000)
    );
    console.log(a, b, c); //1,2,3
}
foo();
```

따라서 3개의 비동기 처리가 서로 연관이 없는 경우에는 다음과 같이 처리해야 한다.

```js
async function foo() {
    const a = await Promise.all[
        (new Promise((resolve) => setTimeout(() => resolve(1), 3000)),
        new Promise((resolve) => setTimeout(() => resolve(1), 3000)),
        new Promise((resolve) => setTimeout(() => resolve(1), 3000)))
    ];
}
console.log(a, b, c); //1,2,3
foo();
```
