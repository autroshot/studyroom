# 브라우저: 문서, 이벤트, 인터페이스
브라우저 내 페이지를 다루는 방법에 대해 학습한다. 요소 추가, 요소의 사이즈와 위치를 조정하는 방법을 비롯하여 동적으로 인터페이스를 생성하는 방법, 인터페이스를 기반으로 사용자와 상호작용 하는 방법 등에 대해 배울 것이다.
## [01] 문서
### 1. [브라우저 환경과 다양한 명세서](./01-문서/01-브라우저-환경과-다양한-명세서.md)
- 문서 객체 모델(DOM)
- 브라우저 객체 모델(BOM)
### 2. [DOM 트리](./01-문서/02-DOM-트리.md)
### 3. [DOM 탐색하기](./01-문서/03-DOM-탐색하기.md)
- 트리 상단의 documentElement와 body
- childNodes, firstChild, lastChild로 자식 노드 탐색하기
- 형제와 부모 노드
- 요소 간 이동
- 테이블 탐색하기
### 4. [getElement\*, querySelector\*로 요소 검색하기](./01-문서/04-getElement와-querySelector로-요소-검색하기.md)
- document.getElementById 혹은 id를 사용해 요소 검색하기
- querySelectorAll
- querySelector
- matches
- closest
- getElementsBy*
- 살아있는 컬렉션
### 5. [주요 노드 프로퍼티](./01-문서/05-주요-노드-프로퍼티.md)
- DOM 노드 클래스
- ‘nodeType’ 프로퍼티
- nodeName과 tagName으로 태그 이름 확인하기
- innerHTML로 내용 조작하기
- outerHTML로 요소의 전체 HTML 보기
- nodeValue/data로 텍스트 노드 내용 조작하기
- textContent로 순수한 텍스트만
- hidden 프로퍼티
- 기타 프로퍼티
### 6. [속성과 프로퍼티](./01-문서/06-속성과-프로퍼티.md)
- DOM 프로퍼티
- HTML 속성
- 프로퍼티-속성 동기화
- DOM 프로퍼티 값의 타입
- 비표준 속성, dataset
### 7. [문서 수정하기](./01-문서/07-문서-수정하기.md)
- 요소 생성하기
- 삽입 메서드
- insertAdjacentHTML/Text/Element
- 노드 삭제하기
- cloneNode로 노드 복제하기
- DocumentFragment
- 구식 삽입·삭제 메서드
- 'document.write’에 대한 첨언
### 8. [스타일과 클래스](./01-문서/08-스타일과-클래스.md)
- className과 classList
- 요소의 스타일
- style 프로퍼티 재지정하기
- 단위에 주의하기
- getComputedStyle로 계산된 스타일 얻기
### 9. [요소 사이즈와 스크롤](./01-문서/09-요소-사이즈와-스크롤.md)
- 기하 프로퍼티
- offsetParent와 offsetLeft, offsetTop
- offsetWidth와 offsetHeight
- clientTop과 clientLeft
- clientWidth와 clientHeight
- scrollWidth와 scrollHeight
- scrollLeft와 scrollTop
- CSS에서 너비와 높이를 얻지 말아야 한다
### 10. [브라우저 창 사이즈와 스크롤](./01-문서/10-브라우저-창-사이즈와-스크롤.md)
- 브라우저 창의 너비와 높이
- 문서의 너비와 높이
- 스크롤 정보 얻기
- scrollTo, scrollBy로 스크롤 상태 변경하기
- scrollIntoView
- 스크롤 막기
### 11. [좌표](./01-문서/11-좌표.md)
- getBoundingClientRect로 요소 좌표 얻기
- elementFromPoint(x, y)
- 요소를 창 내 특정 좌표에 고정하기
- 문서 기준 좌표

## [02] 이벤트 기초
### 1. [브라우저 이벤트 소개](./02-이벤트-기초/01-브라우저-이벤트-소개.md)
- 이벤트 핸들러
- this로 요소에 접근하기
- 자주 하는 실수
- addEventListener
- 이벤트 객체
- 객체 형태의 핸들러와 handleEvent
### 2. [버블링과 캡처링](./02-이벤트-기초/02-버블링과-캡처링.md)
- event.target
### 3. [이벤트 위임](./02-이벤트-기초/03-이벤트-위임.md)
- '행동' 패턴
### 4. [브라우저 기본 동작](./02-이벤트-기초/04-브라우저-기본-동작.md)
- addEventListener의 ‘passive’ 옵션
- event.defaultPrevented
### 5. [커스텀 이벤트 디스패치](./02-이벤트-기초/05-커스텀-이벤트-디스패치.md)
- Event 생성자
- dispatchEvent
- MouseEvent, KeyboardEvent를 비롯한 다양한 UI 이벤트
- event.preventDefault()
- 이벤트 안의 이벤트는 동기적이다

## [03] UI 이벤트
- 미번역
- 개요: 마우스, 드래그 앤 드롭, 포인터(모던 마우스), 키보드, 스크롤링 관련 이벤트에 대해 배운다.

## [04] 폼과 폼 조작
### 1. [폼 프로퍼티와 메서드](./04-폼과-폼-조작/01-폼-프로퍼티와-메서드.md)
- 폼과 요소 탐색하기
- element.form으로 역참조 하기
- 폼 요소
### 2. [focus와 blur](./04-폼과-폼-조작/02-focus와-blur.md)
- tabindex를 사용해서 모든 요소 포커스 하기
- focusin과 focusout을 사용해 이벤트 위임하기
### 3. [이벤트: change, input, cut, copy, paste](./04-폼과-폼-조작/03-이벤트-change-input-cut-copy-paste.md)
### 4. [submit 이벤트와 메서드](./04-폼과-폼-조작/04-submit-이벤트와-메서드.md)

## [05] 문서와 리소스 로딩
### 1. [페이지 관련 이벤트: DOMContentLoaded, load, beforeunload, unload](./05-문서와-리소스-로딩/01-DOMContentLoaded-load-beforeunload-unload-이벤트.md)
- readyState
### 2. [defer, async 스크립트](./05-문서와-리소스-로딩/02-defer-async-스크립트.md)
- 동적 스크립트
### 3. [Resource loading: onload and onerror](https://ko.javascript.info/onload-onerror)
- 미번역
- 개요: `onload`와 `onerror` 이벤트를 이용해 외부 리소스(스크립트, iframe, 그림)의 로딩을 추적할 수 있다.

## [06] 기타
### 1. [Mutation observer](https://ko.javascript.info/mutation-observer)
- 미번역
- 개요: `MutationObserver`는 DOM 요소를 관찰하고 변경을 감지하면 콜백을 실행하는 내장 객체이다.
### 2. [Selection and Range](https://ko.javascript.info/selection-range)
- 미번역
- 개요: 마우스 드래그나 키보드에 의해 문서에서 선택된 텍스트를 다룰 때 사용한다.

### 3. [이벤트 루프: 마이크로태스크와 매크로태스크](./06-기타/03-이벤트-루프-마이크로태스크-매크로태스크.md)
