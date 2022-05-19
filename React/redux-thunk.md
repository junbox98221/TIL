# redux thunk

> 출처 [리액트를 다루는 기술](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791160508796)을 보고 정리한 내용입니다.

redux-thunk는 리덕스를 사용하는 프로젝트에서 비동기 작업을 처리할 때 기본적으로 사용하는 미들웨어이다.

thunk는 특정 작업을 나중에 할 수 있도록 미루기 위해 함수 형태로 감싼 것을 의미한다.

```js
const addOne = (x) => x + 1;
//함수 선언문
function addOnThunk(x) {
    const thunk = addOne(x);
    return thunk;
}
//화살표 함수
const  addOneThunk = x => () => addOne(x);

cosnt fn = addOneThunk(1);
setTimeout(()=>{
    const value = fn();
    console.log(value);
},1000);
```

redux-thunk를 사용할 땐 스토어에 redux-thunk를 적용해야 한다.

```js
const store = createStore(rootReducer, applyMiddleware(ReduxThunk));
```

redux-thunk는 redux에서 액션 생성 함수가 액션 객체를 반환하는 대신에 함수를 반환한다.

아래는 앞서 언급한대로 비동기 처리를 할 때 액션 함수와 리듀서의 예시이다.

```js
const GET_POST = "sample/GET_POST";
const GET_POST_SUCCESS = "sample/GET_POST_SUCCESS";
const GET_POST_FAILURE = "sample/GET_POST_FAILURE";

export const getPost = (id) => async (dispatch) => {
    //thunk 함수(기존 액션 생성 함수와 다르게 함수를 반환한다)
    dispatch({ type: GET_POST });
    try {
        //loading 여부까지 확인한다.
        const response = await api.getPost.getPost(id);
        dispatch({ type: GET_POST_SUCCESS, payload: response.data });
    } catch (e) {
        dispatch({
            type: GET_POST_FAILURE,
            payload: e,
            error: true,
        });
        throw e;
    }
};

const initialState = {
    loading: {
        GET_POST: false,
    },
    post: null,
};

const sample = handleActions(
    {
        [GET_POST]: (state) => ({
            ...state,
            loading: {
                ...state.loading,
                GET_POST: true,
            },
        }),
        [GET_POST_SUCCESS]: (state, action) => ({
            ...state,
            loading: {
                ...state.loading,
                GET_POST: true,
            },
            post: action.payload,
        }),
        [GET_POST_FAILURE]: (state) => ({
            ...state,
            loading: {
                ...state.loading,
                GET_POST: false,
            },
        }),
    },
    initialState
);

export default sample;
```

위에선 1개의 예시였지만 thunk 함수를 여러 개 작성해야 하는 경우 thunk 함수 작성 부분을 분리하여 코드의 양을 줄인다. 아래는 로딩 관련 로직까지 포함되어 있다.

```js
export default function createRequestThunk(type, request) {
    const SUCCESS = `${type}_SUCCESS`;
    const FAILURE = `${type}_FAILURE`;

    return (params) => async (dispatch) => {
        dispatch({ type });
        try {
            const response = await request(params);
            dispatch({ type: SUCCESS, payload: response.data });
        } catch (e) {
            dispatch({ type: FAILURE, payload: e, error: true });
            throw e;
        }
    };
}
```

이와 같이 hook을 적용하면 위의 액션 함수가 아래와 같이 단축된다.

```js
//1. createRequestThunk로 thunk 함수(액션 생성 함수) 리팩토링
const GET_POST = "sample/GET_POST";
const GET_POST_SUCCESS = "sample/GET_POST_SUCCESS";
const GET_POST_FAILURE = "sample/GET_POST_FAILURE";

export const getPost = createRequestThunk(GET_POST, api.getPost);

const initialState = {
    loading: {
        GET_POST: false,
    },
    post: null,
};

const sample = ....
```

이번에는 로딩에 대한 코드도 리팩토링해보자.

```js
import { handleActions } from "redux-actions";
import { createAction } from "redux-actions";

const START_LOADING = "LOADING/START_LOADING";
const FINISH_LOADING = "loading/FINISH_LOADING";

export const startLoading = createAction(
    START_LOADING,
    (requestType) => requestType
);
export const finishLoading = createAction(
    FINISH_LOADING,
    (requestType) => requestType
);

const initialState = {};

const loading = handleActions(
    {
        [START_LOADING]: (state, action) => ({
            ...state,
            [action.payload]: false,
        }),
        [FINISH_LOADING]: (state, action) => ({
            ...state,
            [action.payload]: true,
        }),
    },
    initialState
);

export default loading;
```

여기서 만든 loading 관련 reducer를 rootReducer에 포함시킨다.

```js
import { combineReducers } from "redux";
import counter from "./counter";
import todos from "./todos";

const rootReducer = combineReducers({
    sample,
    loading,
});

export default rootReducer;
```

이제 createRequestThunk에 적용된 로딩 코드들을 대체한다. dispatch 시작 직후와 성공 및 실패 이후에 loading dispatch를 실행하고 있다.

```js
import { startLoading, finishLoading } from "../modules/loading";

export default function createRequestThunk(type, request) {
    const SUCCESS = `${type}_SUCCESS`;
    const FAILURE = `${type}_FAILURE`;

    return (params) => async (dispatch) => {
        dispatch({ type });
        dispatch(startLoading(type));
        try {
            const response = await request(params);
            dispatch({ type: SUCCESS, payload: response.data });
            dispatch(finishLoading(type));
        } catch (e) {
            dispatch({ type: FAILURE, payload: e, error: true });
            dispatch(finishLoading(type));
            throw e;
        }
    };
}
```

최종으로 sample리듀서에서 불필요한 코드를 제거한다.

```js
import handleAction from "redux-actions";
import { handleActions } from "redux-actions";
import createRequestThunk from "../lib/createRequestThunk";

import * as api from "../lib/api";

const GET_POST = "sample/GET_POST";
const GET_POST_SUCCESS = "sample/GET_POST_SUCCESS";
const GET_POST_FAILURE = "sample/GET_POST_FAILURE";

export const getPost = createRequestThunk(GET_POST, api.getPost);

//loading에 대한 initialState 삭제됨
const initialState = {
    post: null,
    users: null,
};

const sample = handleActions(
    {
        //loading에 대한 코드 삭제됨
        [GET_POST]: (state) => ({
            ...state,
        }),
        [GET_POST_SUCCESS]: (state, action) => ({
            ...state,
            post: action.payload,
        }),
        [GET_POST_FAILURE]: (state) => ({
            ...state,
        }),
    },
    initialState
);

export default sample;
```
