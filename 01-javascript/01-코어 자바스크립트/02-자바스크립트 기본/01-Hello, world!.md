# Hello, world!
\<script\>의 속성(attribute)은 다음과 같다.
- type: 모듈에 사용된다.
- language: 현재는 사용되지 않는다.
- **src: 외부 스크립트의 경로를 나타낸다.**

```html
<script src="/path/to/script.js"></script>
```
외부 스크립트를 사용하면 여러 페이지에서 동일한 스크립트를 사용했을 때 성능 상의 이점이 있다.

---
:warning: **src 속성이 있으면 태그 내부의 코드는 무시된다.**

\<script\> 태그는 src 속성과 내부 코드를 동시에 가질 수 없다.
```html
<script src="file.js">
  alert(1); // src 속성이 사용되었으므로 이 코드는 무시된다.
</script>
```
---
