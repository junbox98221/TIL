# data fetching

> 출처 [블로그](https://fe-developers.kakaoent.com/2022/220224-data-fetching-libs/)을 보고 정리한 내용입니다.

리액트 프로젝트에서 사용하는 상태를 세 가지로 구분하자.

-   Local State: 리액트 컴포넌트 안에서만 사용되는 state
-   Global State: Global Store에 정의되어 프로젝트 어디에서나 접근할 수 있는 state(redux...)
-   Server State: 서버로부터 받아오는 state

기존에는 리덕스와 같은 상태 관리 라이브러리에서 Global State와 Server State를 전부 포함하는 방법으로 프로그래밍을 했지만 data fetching 라이브러리를 사용함으로써 상태 관리 라이브러리에서 비동기 로직을 제거하여 관심사가 분리되고 선언적으로 프로그래밍할 수 있게 되었다.

---

## 리덕스에서 server state 처리

리덕스는 액션(순수 객체)가 발생하면 액션에 따라 데이터의 상태가 어떻게 변경되는지를 리듀서(순수 함수)에 정의한다.

이때 비동기 로직(server state)를 처리하기 위해서는 미들웨어인 redux-thunk, redux-saga를 사용한다.

redux-saga를 이용하면 코드가 장황해지고 global state, server state를 하나의 스토어에서 관리하며 스토어는 점점 비대해지고 관심사의 분리가 어려워진다.

```js
function* getSomethingSaga() {
    try {
        const res = yield call(fetchSomething);

        yield put({
            type: "successAction",
            payload: res.data,
        });
    } catch {
        yield put({
            type: "failAction",
            payload: res.data,
        });
    }
}

function* someSaga() {
    yield takeEvery("someAction", getSomethingSaga);
}

function* rootSaga() {
    yield all[someSaga()];
}

store.dispatch({ type: "someAction" });
```

## SWR & React Query

data fetching 라이브러리는 리덕스의 단점을 해결한다.

-   선언적으로 프로그래밍 할 수 있음(장황하지 않은 코드)
-   동일한 API 요청이 여러 번 호출될 경우 한 번만 실행(redux-saga는 예외)
-   데이터가 dirty 해진 경우 적절한 시점에 알아서 업데이트
-   Global State와 Server State의 관심사를 분리

```js
import { useQuery } from "react-query";

const useUser = (id) => {
    const result = useQuery(`/user/${id}`, fetcher, options);

    return result;
};

function Example() {
    const { isLoading, error, data } = useUser("123");

    if (isLoading) return "Loading...";
    if (error) return "An error has occurred: " + error.message;
    return <div>hello {data.name}!</div>;
}
```

react-query 사용법은 첫 번째 인자에 URL, 두 번째 인자에 fetcher 함수(axios...) , 3번쨰 인자에 options를 설정한다.

data fetching 라이브러리를 사용하면, 쉽게 데이터를 가져올 수 있고 동일한 요청을 하는 여러 컴포넌트가 동시에 렌더링 되더라도 한 번만 요청한다.

또한, 백그라운드에서 서버에 주기적으로 polling을 하면서 데이터가 유효한지 검사하고, 유효하지 않으면 업데이트하여 데이터를 동기화한다. 위에서 언급한 리덕스를 구현할 때 고려해야 할 점들이 다 사라진다.

좀 더 학습이 필요한 것 같다. 정리하자면 비동기 처리를 위한 redux-saga의 사이드 이펙트를 관리해야 하는 부분과 서버의 응답을 store에 저장할 필요가 없는 상황에도 구조화를 위해 action type, actions, reducer를 만들고 action을 호출하는 상황의 문제점, 서버와 클라이언트 데이터 구분의 모호함을 방지하기 위해 사용한다.

따라서 redux, redux-saga의 조합을 고집하기 보다는 react-query와 다른 상태관리 라이브러리를 사용해도 좋다.
