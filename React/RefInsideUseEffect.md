# useRef와 useEffect 조합의 문제

> 출처 [블로그](https://medium.com/@teh_builder/ref-objects-inside-useeffect-hooks-eb7c15198780)을 보고 정리한 내용입니다.

DOM 노드를 참조하고 노드의 width에 따라 특정 함수를 실행시키는 로직을 구현하기 위해 useRef와 useEffect를 조합했다. 보통 아래와 같이 코드를 작성할 것이다.

```js
const RefExample = () => {
    const ref = useRef(null);

    useEffect(() => {
        if(ref.current.clientWidth > 50) {
            ...
        }
    }, [ref.current]);

    return (
        <div ref={ref} className="w-10 h-10 bg-red-50">
            ref node
        </div>
    );
};
```

하지만 useEffect 의존성 배열에 ref.current를 추가해도 ref의 변화를 감지하지 못한다.(또 다른 예시이다. https://codesandbox.io/s/01m0ok09rv?from-embed=&file=/src/index.js:141-153)

react 개발자의 트위터 상에서 답변으로는 마법처럼 ref 변화를 감지할 수 없고 ref는 컴포넌트의 라이프 사이클과는 독립적으로 존재하기 때문에 이는 적절한 접근법이 아니라고 한다. (https://twitter.com/dan_abramov/status/1093497348913803265?ref_src=twsrc%5Etfw%7Ctwcamp%5Etweetembed%7Ctwterm%5E1093497348913803265%7Ctwgr%5E11ae973ca907d1cdee9d12f689684abcafce5a7d%7Ctwcon%5Es1_&ref_url=https%3A%2F%2Fcdn.embedly.com%2Fwidgets%2Fmedia.html%3Ftype%3Dtext2Fhtmlkey%3Da19fcc184b9711e1b4764040d3dc5c07schema%3Dtwitterurl%3Dhttps3A%2F%2Ftwitter.com%2Fdan_abramov%2Fstatus%2F1093497348913803265image%3Dhttps3A%2F%2Fi.embed.ly%2F1%2Fimage3Furl3Dhttps253A252F252Fpbs.twimg.com252Fprofile_images252F906557353549598720252FoapgW_Fp_400x400.jpg26key3Da19fcc184b9711e1b4764040d3dc5c07)

react 문서에도 감지하지 못하는 이유가 나와있진 않고 DOM node의 변화를 감지하고 싶다면 useCallback, [ResizeObserver](https://developer.mozilla.org/en-US/docs/Web/API/ResizeObserver)를 사용하는 방식을 안내한다. (https://legacy.reactjs.org/docs/hooks-faq.html#how-can-i-measure-a-dom-node)

아래 제시한 해결책을 보면 usecallback 훅으로 DOM node를 참조할 변수를 선언한다.

```js
function MeasureExample() {
    const [height, setHeight] = useState(0);

    const measuredRef = useCallback((node) => {
        if (node !== null) {
            setHeight(node.getBoundingClientRect().height);
        }
    }, []);

    return (
        <>
            <h1 ref={measuredRef}>Hello, world</h1>
            <h2>The above header is {Math.round(height)}px tall</h2>
        </>
    );
}
```

훅의 내부 구조까지 파악하며 확인을 하지 않아 해결책을 제시한 것 뿐이지만 DOM 노드의 변화를 참조하여 어떤 로직을 실행하려 할 때 useRef를 사용할 수 없을 뿐더러 기존 useRef로 DOM 노드를 참조하던 역할을 useCallback 훅이 대체할 수 있기 떄문에 useRef훅을 그다지 사용하지 않을 것 같다.
