# redux-saga

> 출처 [리액트를 다루는 기술](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791160508796)을 보고 정리한 내용입니다.

비동기 작업을 관리하는 대부분의 경우 redux-thunk로 충분히 기능을 구현할 수 있다.

redux-saga를 사용하는 이유는 다음과 같은 까다로운 상황에 유용하기 때문이다.

-   기존 요청을 취소 처리해야 할 때(불필요한 중복 요청 방지)
-   특정 액션이 발생했을 때 다른 액션을 발생시키거나, api 요청 등 리덕스와 관계없는 코드를 실행할 때
-   웹소켓을 사용할 때
-   api 요청이 실패한 경우 재요청할 때

redux-thunk를 사용할 때와 마찬가지로 스토어에 redux-saga 미들웨어를 적용한다.

```js
import createSagaMiddleWare from "redux-saga";

const sagaMiddleware = createSagaMiddleWare();
const store = createStore(rootReducer, applyMiddleware(sagaMiddleware));
sagaMiddleware.run(rootSaga);
```

다음은 api 요청을 redux-saga를 사용한 예시이다. loading 은 요청 시작 후 응답까지 loading status를 관리한다. 신경쓰지 말자.

```js
import { createAction, handleActions } from "redux-actions";
import { call, put, takeLatest } from "redux-saga/effects";
import * as api from "../lib/api";
import createRequestSaga from "../lib/createRequestSaga";
import { finishLoading, startLoading } from "./loading";

// 액션 타입 선언
const GET_POST = "sample/GET_POST";
const GET_POST_SUCCESS = "sample/GET_POST_SUCCESS";
const GET_POST_FAILURE = "sample/GET_POST_FAILURE";

export const getPost = createAction(GET_POST, (id) => id);
export const getUsers = createAction(GET_USERS);

const getPostSaga = createRequestSaga(GET_POST, api.getPost);
const getUsersSaga = createRequestSaga(GET_USERS, api.getUsers);

function* getPostSaga(action) {
    yield put(startLoading(GET_POST)); // 로딩 시작
    // 파라미터로 action 을 받아오면 액션의 정보를 조회 할 수 있다
    try {
        // call 을 사용하면 Promise 를 반환하는 함수를 호출하고, 기다릴 수 있습니다.
        // 첫번째 파라미터는 함수, 나머지 파라미터는 해당 함수에 넣을 인수입니다.
        const post = yield call(api.getPost, action.payload); // api.getPost(action.payload) 를 의미
        yield put({
            type: GET_POST_SUCCESS,
            payload: post.data,
        });
    } catch (e) {
        // try/catch 문을 사용하여 에러도 잡을 수 있습니다.
        yield put({
            type: GET_POST_FAILURE,
            payload: e,
            error: true,
        });
    }
    yield put(finishLoading(GET_POST)); // 로딩 완료
}

// takeLastest는 마지막에 실행된 자업만 수행한다.
export function* sampleSaga() {
    yield takeLatest(GET_POST, getPostSaga);
}

// 초기 상태를 선언합니다.
// 요청의 로딩중 상태는 loading 이라는 객체에서 관리합니다.

const initialState = {
    post: null,
};

const sample = handleActions(
    {
        [GET_POST_SUCCESS]: (state, action) => ({
            ...state,
            post: action.payload,
        }),
    },
    initialState
);

export default sample;
```

react-thunk에서 thunk함수를 따로 만들었듯이 여기서도 createRequestSaga함수를 만들어 리팩토링할 수 있다.

```js
import { call, put } from "redux-saga/effects";
import { startLoading, finishLoading } from "../modules/loading";

export default function createRequestSaga(type, request) {
    const SUCCESS = `${type}_SUCCESS`;
    const FAILURE = `${type}_FAILURE`;

    return function* (action) {
        yield put(startLoading(type)); // 로딩 시작
        try {
            const response = yield call(request, action.payload);
            yield put({
                type: SUCCESS,
                payload: response.data,
            });
        } catch (e) {
            yield put({
                type: FAILURE,
                payload: e,
                error: true,
            });
        }
        yield put(finishLoading(type)); // 로딩 끝
    };
}
```
