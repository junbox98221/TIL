# call/apply와 데코레이터, 포워딩

> 출처 [모던 JavaScript 튜토리얼](https://ko.javascript.info/)을 보고 정리한 내용입니다.

인수로 받은 함수의 행동을 변견시켜주는 함수를 데코레이터라고 부른다. 아래 예시에서 cachingDecorator함수는 cache에 키가 저장되어 있는지 여부에 따라 인수로 받은 slow함수의 행동을 바꾼다.

```
function slow(x) {
  // CPU 집약적인 작업이 여기에 올 수 있습니다.
  alert(`slow(${x})을/를 호출함`);
  return x;
}

function cachingDecorator(func) {
  let cache = new Map();

  return function(x) {
    if (cache.has(x)) {    // cache에 해당 키가 있으면
      return cache.get(x); // 대응하는 값을 cache에서 읽어옵니다.
    }

    let result = func(x);  // 그렇지 않은 경우엔 func를 호출하고,

    cache.set(x, result);  // 그 결과를 캐싱(저장)합니다.
    return result;
  };
}

slow = cachingDecorator(slow);

alert( slow(1) ); // slow(1)이 저장되었습니다.
alert( "다시 호출: " + slow(1) ); // 동일한 결과

alert( slow(2) ); // slow(2)가 저장되었습니다.
alert( "다시 호출: " + slow(2) ); // 윗줄과 동일한 결과
```

cpu를 많이 소모하지만 결과가 안정적인 함수 slow(x)의 재연산 시간을 줄이기 위해 사용했는데 이렇게 slow함수 안에서 캐싱 관련 코드를 추가하는 대신 래퍼 함수를 만들어 캐싱 기능을 추가하면

-   cachingDecorator를 재사용 가능
-   캐싱 로직이 분리되어 slow자체의 복잡성이 증가하지 않음
-   필요시 여러 데코레이털르 조합해 사용가능

```
// worker.slow에 캐싱 기능을 추가해봅시다.
let worker = {
  someMethod() {
    return 1;
  },

  slow(x) {
    // CPU 집약적인 작업이라 가정
    alert(`slow(${x})을/를 호출함`);
    return x * this.someMethod(); // (*)
  }
};

// 이전과 동일한 코드
function cachingDecorator(func) {
  let cache = new Map();
  return function(x) {
    if (cache.has(x)) {
      return cache.get(x);
    }
    let result = func(x); // (**)
    cache.set(x, result);
    return result;
  };
}

alert( worker.slow(1) ); // 기존 메서드는 잘 동작합니다.

worker.slow = cachingDecorator(worker.slow); // 캐싱 데코레이터 적용

alert( worker.slow(2) ); // 에러 발생!, Error: Cannot read property 'someMethod' of undefined
```

객체 메서드에 데코레이터를 적용하면 데코레이터의 반환 함수인 worker.slow에서 this가 undefined이기 때문에 func.call로 this를 지정해야 한다.
따라서 위 코드에서 func.call(this,x)로 컨텍스트를 전달해야 한다.

### func.apply

func.call과 유사한데 func.apply는 유사 배열 객체를 받는다. 따라서 인자가 여러 개인 경우를 처리할 때 사용한다.
