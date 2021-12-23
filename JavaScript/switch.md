# switch

> 출처 [모던 JavaScript 튜토리얼](https://ko.javascript.info/)을 보고 정리한 내용입니다.

switch문은 변수를 다양한 상황에서 비교할 때 효과적이다.

```
let a = 4;

switch (a) {
  case 3:
    alert( '비교하려는 값보다 작습니다.' );
    break;
  case 4:
    alert( '비교하려는 값과 일치합니다.' );
    break;
  case 5:
    alert( '비교하려는 값보다 큽니다.' );
    break;
  default:
    alert( "어떤 값인지 파악이 되지 않습니다." );
}
```

위처럼 case 와 a를 비교하며 내려간다.

그리고 case 4 에서 break를 마주하기 전까지 코드를 실행하고 break를 만나면 switch문을 탈출한다.

만약 break가 없다면 아래 case에 대해서도 비교하기 때문에 break를 삽입하는 것이 권장된다.

```
let arg = prompt("값을 입력해주세요.");
switch (arg) {
  case '0':
  case '1':
    alert( '0이나 1을 입력하셨습니다.' );
    break;

  case '2':
    alert( '2를 입력하셨습니다.' );
    break;

  case 3:
    alert( '이 코드는 절대 실행되지 않습니다!' );
    break;
  default:
    alert( '알 수 없는 값을 입력하셨습니다.' );
}
```

만약 prompt로 '0','1','2'를 전달하면 문자열이기 때문에 case문을 실행하지만 3을 누르면 case문에서는 숫자이기 때문에 실행되지 않는다.

자료형의 중요성을 보여준다.
