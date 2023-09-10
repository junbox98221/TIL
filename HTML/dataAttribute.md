# dataAttribute

> 출처 [mdn](https://developer.mozilla.org/ko/docs/Learn/HTML/Howto/Use_data_attributes)을 보고 정리한 내용입니다.

HTML5부터 데이터 속성이라는 개념이 추가되었다.

data-\* 속성은 특정한 데이터를 DOM 요소에 저장하기 위해 사용된다.

화면에 안 보이게 글이나 추가 정보를 엘리먼트에 담을 수 있다.

```html
<article
    id="electriccars"
    data-columns="3"
    data-index-number="12314"
    data-parent="cars"
>
    ...
</article>
```

js로 접근하기 위해서는 기존 attribute와 동일하게 [getAttribute()](https://developer.mozilla.org/en-US/docs/Web/API/Element/getAttribute)를 사용할 수 있다.

하지만 더 간단한 방법이 제공된다.

```js
var article = document.getElementById("electriccars");

article.dataset.columns; // "3"
article.dataset.indexNumber; // "12314"
article.dataset.parent; // "cars"
```

데이터 속성은 HTML 속성이기 때문에 CSS에서도 접근할 수 있다.

```css
article::before {
    content: attr(data-parent);
}

a[data-columns="3"] {
    color: purple;
}
```
