# 블로그

> 출처 [블로그](https://kentcdodds.com/blog/what-is-jsx)을 보고 정리한 내용입니다.

JSX는 React.createElement를 호출하기 위한 문법 설탕이다. JSX는 아래처럼 React.createElement로 컴파일된다.

```js
ui = <div id="root">Hello world</div>;

ui = React.createElement("div", { id: "root" }, "Hello world");
```

createElement는 3가지를 인자로 받는다.

-   elementType : 생성할 요소 유형에 대한 문자열 또는 함수
-   props : 요소에 적용하려는 객체
-   ...children : 엘리먼트에 적용될 자식 요소

```js
function createElement(elementType, props, ...children) {}
```

React.createElement는 객체를 반환한다.

```js
{
  type: "div",
  key: null,
  ref: null,
  props: { id: "root", children: "Hello world" },
  _owner: null,
  _store: {}
};

```

ReactDOM.render에 해당 엘리먼트 객체를 넘기면 이를 해석해서 DOM.node를 만든다.

```js
ui = <div>Hello {subject}</div>;
ui = React.createElement("div", null, "Hello ", subject);

ui = (
    <div>
        {greeting} {subject}
    </div>
);
ui = React.createElement("div", null, greeting, " ", subject);

ui = <button onClick={() => {}}>click me</button>;
ui = React.createElement("button", { onClick: () => {} }, "click me");

ui = <div>{error ? <span>{error}</span> : <span>good to go</span>}</div>;
ui = React.createElement(
    "div",
    null,
    error
        ? React.createElement("span", null, error)
        : React.createElement("span", null, "good to go")
);

ui = (
    <div>
        {items.map((i) => (
            <span key={i.id}>{i.content}</span>
        ))}
    </div>
);
ui = React.createElement(
    "div",
    null,
    items.map((i) => React.createElement("span", { key: i.id }, i.content))
);
```

JSX는 React.createElement로 컴파일되고 React.createElement는 결국 객체로 반환된다. 그렇기 때문에 jsx를 if 구문 및 for loop 안에 사용하고, 변수에 할당하고, 인자로서 받아들이고, 함수로부터 반환할 수 있는 것이다.
