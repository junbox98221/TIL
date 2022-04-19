# state

> 출처 [리액트를 다루는 기술](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791160508796)을 보고 정리한 내용입니다.

컴포넌트 내부에서 변경 가능한 데이터를 관리해야 할 때 state를 사용해야 한다.

아래 예시를 보면 let으로 선언한 변수도 의도한 대로 버튼을 클릭하면 값이 1씩 증가하지만 변경된 값이 화면에는 반영되지 않는데,
react에서는 state에 의해 선언된 변수의 값이 변경되었을 때 리렌더링을 하기 때문이다.

<img
    src="image/state/add.png
"
    width="1200"
    height="250"
  />
