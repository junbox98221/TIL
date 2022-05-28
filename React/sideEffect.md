# side Effect

> 출처 [리액트를 다루는 기술](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791160508796)을 보고 정리한 내용입니다.

side effect란 함수가 실행되면서 함수 외부에 존재하는 값이나 상태를 변경시키는 행위를 말한다.

예를 들어 전역변수의 값 혹은 함수 외부에 존재하는 텍스트, 파일 쓰기, 쿠키 저장, 네트워크로 데이터 송신등이 있다.

react에서 side effect가 있는 함수의 예시는 다음과 같은데 권장되지 않는다.

```js
function UserProfile({ name }) {
    const message = `${name}님 환영합니다!`; //함수 반환 값 생성

    // Bad!
    document.title = `${name}의 개인정보`; //함수 외부와 상호작용하는 Side-effect 코드
    return <div>{message}</div>;
}
```

이 코드는 함수형 컴포넌트가 실행되고 결과를 생성하는 것과 무관한 document.title을 수정한다.

만약 document.title을 수정하는 것 대신에 무거운 작업을 수행하는 코드였다면, 컴포넌트가 렌더링 될 때마다 지연되기 때문에 react에서는 useEffect를 통해 side effect를 분리하도록 지원한다.

아래 예시이다.

```js
function UserProfile({ name }) {
    const message = `${name}님 환영합니다!`;

    //Side-Effect 코드를 UseEffect로 분리
    useEffect(() => {
        document.title = `${name}의 개인정보`;
    }, [name]);
    return <div>{message}</div>;
}
```
