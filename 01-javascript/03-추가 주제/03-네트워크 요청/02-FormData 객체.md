# FormData 객체

이번 챕터에서는 HTML 폼(form) 전송에 대해 알아볼 것이다.

[FormData](https://xhr.spec.whatwg.org/#interface-formdata) 객체를 이용하면 된다. 이름을 보면 알 수 있듯이 `FormData` 객체는 HTML 폼 데이터를 나타낸다.

생성자는 다음과 같다.
```js
let formData = new FormData([form]);
```
생성자의 인자에 HTML `form` 요소를 넘겨주면  해당 폼의 필드가 자동으로 캡쳐된다.

`fetch` 등의 네트워크 메서드는 `FormData` 객체를 바디로 받을 수 있다. 메서드는 자동으로 객체를 인코딩해서 `Content-Type: multipart/form-data`와 함께 요청을 보낸다.

서버는 이것을 일반적인 폼 제출처럼 인식한다.

## 간단한 폼 전송하기

먼저 간단한 폼을 전송해 보겠다.
```html
<form id="formElem">
  <input type="text" name="name" value="Bora">
  <input type="text" name="surname" value="Lee">
  <input type="submit">
</form>

<script>
  formElem.onsubmit = async (e) => {
    e.preventDefault();

    let response = await fetch('/article/formdata/post/user', {
      method: 'POST',
      body: new FormData(formElem)
    });

    let result = await response.json();

    alert(result.message);
  };
</script>
```
서버 코드는 범위를 벗어나므로 생략되었다. 서버는 POST 요청을 수락하고 성공했다는 응답을 보내줄 것이다.

## FormData 메서드

`FormData`의 필드는 다음의 메서드로 수정할 수 있다.

|메서드|설명|
|---|---|
|`formData.append(name, value)`|`name`과 `value`를 가진 폼 필드를 추가|
|`formData.append(name, blob, fileName)`|`<input type="file">` 형태의 필드를 추가. 세 번째 인수 `fileName`은 필드 이름이 아니라 파일 이름을 설정|
|`formData.delete(name)`|`name`에 해당하는 필드를 삭제|
|`formData.get(name)`|`name`에 해당하는 필드의 값을 가져옴|
|`formData.has(name)`|`name`에 해당하는 필드가 있으면 `true`를, 그렇지 않으면 `false`를 반환|

폼은 기술적으로 `name`이 같은 복수의 필드를 허용하기 때문에 `append` 메서드를 여러 번 호출하면 이름이 같은 필드가 추가된다.

`append` 동일한 문법을 가진 `set` 메서드도 있다. 차이점은 `set`은 `name`이 같은 모든 필드를 삭제한 뒤에 새로운 필드를 추가한다는 것이다. 따라서 `set` 메서드를 쓰면 `name`을 가진 필드가 단 한 개만 존재하게 된다.
- `formData.set(name, value)`
- `formData.set(name, blob, fileName)`

`FormData`의 필드를 순회할 때는 `for..of` 루프를 사용할 수 있다.
```js
let formData = new FormData();
formData.append('key1', 'value1');
formData.append('key2', 'value2');

// key/value 쌍이 담긴 리스트
for(let [name, value] of formData) {
  alert(`${name} = ${value}`); // key1 = value1, then key2 = value2
}
```

## 파일이 있는 폼 전송하기

폼은 항상 `Content-Type: multipart/form-data`로 전송되며, 이 인코딩은 파일을 지원한다. 따라서 일반적인 폼 제출과 동일하게 `<input type="file">` 필드도 정상적으로 전송된다.

예시:
```html
<form id="formElem">
  <input type="text" name="firstName" value="Bora">
  Picture: <input type="file" name="picture" accept="image/*">
  <input type="submit">
</form>

<script>
  formElem.onsubmit = async (e) => {
    e.preventDefault();

    let response = await fetch('/article/formdata/post/user-avatar', {
      method: 'POST',
      body: new FormData(formElem)
    });

    let result = await response.json();

    alert(result.message);
  };
</script>
```

## Blob 데이터가 있는 폼 전송하기

이전 챕터에서 배웠듯이 동적으로 생성된 바이너리 데이터(예: 이미지)는 `Blob`으로 쉽게 전송할 수 있다. `Blob` 객체는 `fetch` 메서드의 `body` 매개변수에 바로 넘겨줄 수 있다.

그런데 실제 개발에서는 이미지를 별도로 보내는 것보다 폼의 일부로 보내는 것이 더 편리하다.

또한 서버도 원시 바이너리 데이터보다 multipart-encoded 폼을 받는 것에 더 최적화되어 있다.

다음 예시에서는 `<canvas>`의 이미지를 `FormData`를 이용해 다른 필드와 함께 하나의 폼으로 제출한다.
```html
<body style="margin:0">
  <canvas id="canvasElem" width="100" height="80" style="border:1px solid"></canvas>
  <input type="button" value="이미지 전송" onclick="submit()">

  <script>
    canvasElem.onmousemove = function(e) {
      let ctx = canvasElem.getContext('2d');
      ctx.lineTo(e.clientX, e.clientY);
      ctx.stroke();
    };

    async function submit() {
      let imageBlob = await new Promise(resolve => canvasElem.toBlob(resolve, 'image/png'));

      let formData = new FormData();
      formData.append('firstName', 'Bora');
      formData.append('image', imageBlob, 'image.png');

      let response = await fetch('/article/formdata/post/image-form', {
        method: 'POST',
        body: formData
      });
      let result = await response.json();
      alert(result.message);
    }

  </script>
</body>
```
이미지 `Blob`이 추가되는 방식을 주목해야 한다.
```js
formData.append('image', imageBlob, 'image.png');
```
이 코드는 폼에 `<input type="file" name="image">`가 있고, 방문자가 `imageBlob` 데이터(두 번째 인수)를 `"image.png"`(세 번째 인수 참고)라는 이름으로 제출하는 것과 동일하다.

서버는 일반적인 폼 제출을 받은 것처럼 폼 데이터와 파일을 읽는다.

## 요약

[FormData](https://xhr.spec.whatwg.org/#interface-formdata) 객체는 HTML 폼을 캡쳐하고 그 내용을 `fetch`를 비롯한 네트워크 메서드로 제출할 때 사용한다.

`FormData` 객체는 생성자의 인자에 기존의 HTML 폼을 넘겨서 만들거나 처음부터 만들 수 있다.

다음의 메소드를 이용해 필드를 추가할 수 있다.
- `formData.append(name, value)`
- `formData.append(name, blob, fileName)`
- `formData.set(name, value)`
- `formData.set(name, blob, fileName)`

두 가지 특이 사항이 있다.
1. `append`과 다르게 `set` 메서드는 `name`이 같은 필드를 모두 삭제한다.
2. 파일을 보낼 때는 인수가 세 개인 문법이 필요하다. 마지막 인수는 파일 이름이다.

이 외의 메서드는 다음과 같다.
- `formData.delete(name)`
- `formData.get(name)`
- `formData.has(name)`
