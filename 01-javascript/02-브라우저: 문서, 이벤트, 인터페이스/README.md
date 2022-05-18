# 브라우저: 문서, 이벤트, 인터페이스
브라우저 내 페이지를 다루는 방법에 대해 학습한다. 요소 추가, 요소의 사이즈와 위치를 조정하는 방법을 비롯하여 동적으로 인터페이스를 생성하는 방법, 인터페이스를 기반으로 사용자와 상호작용 하는 방법 등에 대해 배울 것이다.
## [01] 문서
### 1. [브라우저 환경과 다양한 명세서](https://github.com/autroshot/studyroom/blob/main/01-javascript/02-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80:%20%EB%AC%B8%EC%84%9C%2C%20%EC%9D%B4%EB%B2%A4%ED%8A%B8%2C%20%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4/01-%EB%AC%B8%EC%84%9C/01-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80%20%ED%99%98%EA%B2%BD%EA%B3%BC%20%EB%8B%A4%EC%96%91%ED%95%9C%20%EB%AA%85%EC%84%B8%EC%84%9C.md#%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80-%ED%99%98%EA%B2%BD%EA%B3%BC-%EB%8B%A4%EC%96%91%ED%95%9C-%EB%AA%85%EC%84%B8%EC%84%9C)
- 문서 객체 모델(DOM)
- 브라우저 객체 모델(BOM)
### 2. [DOM 트리](https://github.com/autroshot/studyroom/blob/main/01-javascript/02-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80:%20%EB%AC%B8%EC%84%9C%2C%20%EC%9D%B4%EB%B2%A4%ED%8A%B8%2C%20%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4/01-%EB%AC%B8%EC%84%9C/02-DOM%20%ED%8A%B8%EB%A6%AC.md#dom-%ED%8A%B8%EB%A6%AC)
### 3. [DOM 탐색하기](https://github.com/autroshot/studyroom/blob/main/01-javascript/02-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80:%20%EB%AC%B8%EC%84%9C%2C%20%EC%9D%B4%EB%B2%A4%ED%8A%B8%2C%20%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4/01-%EB%AC%B8%EC%84%9C/03-DOM%20%ED%83%90%EC%83%89%ED%95%98%EA%B8%B0.md#dom-%ED%83%90%EC%83%89%ED%95%98%EA%B8%B0)
- 트리 상단의 documentElement와 body
- childNodes, firstChild, lastChild로 자식 노드 탐색하기
- 형제와 부모 노드
- 요소 간 이동
- 테이블 탐색하기
### 4. [getElement\*, querySelector\*로 요소 검색하기](https://github.com/autroshot/studyroom/blob/main/01-javascript/02-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80:%20%EB%AC%B8%EC%84%9C%2C%20%EC%9D%B4%EB%B2%A4%ED%8A%B8%2C%20%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4/01-%EB%AC%B8%EC%84%9C/04-getElement*%2C%20querySelector*%EB%A1%9C%20%EC%9A%94%EC%86%8C%20%EA%B2%80%EC%83%89%ED%95%98%EA%B8%B0.md#getelement-queryselector%EB%A1%9C-%EC%9A%94%EC%86%8C-%EA%B2%80%EC%83%89%ED%95%98%EA%B8%B0)
- document.getElementById 혹은 id를 사용해 요소 검색하기
- querySelectorAll
- querySelector
- matches
- closest
- getElementsBy*
- 살아있는 컬렉션
### 5. [주요 노드 프로퍼티](https://github.com/autroshot/studyroom/blob/main/01-javascript/02-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80:%20%EB%AC%B8%EC%84%9C%2C%20%EC%9D%B4%EB%B2%A4%ED%8A%B8%2C%20%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4/01-%EB%AC%B8%EC%84%9C/05-%EC%A3%BC%EC%9A%94%20%EB%85%B8%EB%93%9C%20%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0.md#%EC%A3%BC%EC%9A%94-%EB%85%B8%EB%93%9C-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0)
- DOM 노드 클래스
- ‘nodeType’ 프로퍼티
- nodeName과 tagName으로 태그 이름 확인하기
- innerHTML로 내용 조작하기
- outerHTML로 요소의 전체 HTML 보기
- nodeValue/data로 텍스트 노드 내용 조작하기
- textContent로 순수한 텍스트만
- hidden 프로퍼티
- 기타 프로퍼티
### 6. [속성과 프로퍼티](https://github.com/autroshot/studyroom/blob/main/01-javascript/02-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80:%20%EB%AC%B8%EC%84%9C%2C%20%EC%9D%B4%EB%B2%A4%ED%8A%B8%2C%20%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4/01-%EB%AC%B8%EC%84%9C/06-%EC%86%8D%EC%84%B1%EA%B3%BC%20%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0.md#%EC%86%8D%EC%84%B1%EA%B3%BC-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0)
- DOM 프로퍼티
- HTML 속성
- 프로퍼티-속성 동기화
- DOM 프로퍼티 값의 타입
- 비표준 속성, dataset
### 7. [문서 수정하기](https://github.com/autroshot/studyroom/blob/main/01-javascript/02-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80:%20%EB%AC%B8%EC%84%9C%2C%20%EC%9D%B4%EB%B2%A4%ED%8A%B8%2C%20%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4/01-%EB%AC%B8%EC%84%9C/07-%EB%AC%B8%EC%84%9C%20%EC%88%98%EC%A0%95%ED%95%98%EA%B8%B0.md#%EB%AC%B8%EC%84%9C-%EC%88%98%EC%A0%95%ED%95%98%EA%B8%B0)
- 요소 생성하기
- 삽입 메서드
- insertAdjacentHTML/Text/Element
- 노드 삭제하기
- cloneNode로 노드 복제하기
- DocumentFragment
- 구식 삽입·삭제 메서드
- 'document.write’에 대한 첨언
### 8. [스타일과 클래스](https://github.com/autroshot/studyroom/blob/main/01-javascript/02-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80:%20%EB%AC%B8%EC%84%9C%2C%20%EC%9D%B4%EB%B2%A4%ED%8A%B8%2C%20%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4/01-%EB%AC%B8%EC%84%9C/08-%EC%8A%A4%ED%83%80%EC%9D%BC%EA%B3%BC%20%ED%81%B4%EB%9E%98%EC%8A%A4.md#%EC%8A%A4%ED%83%80%EC%9D%BC%EA%B3%BC-%ED%81%B4%EB%9E%98%EC%8A%A4)
- className과 classList
- 요소의 스타일
- style 프로퍼티 재지정하기
- 단위에 주의하기
- getComputedStyle로 계산된 스타일 얻기
### 9. [요소 사이즈와 스크롤](https://github.com/autroshot/studyroom/blob/main/01-javascript/02-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80:%20%EB%AC%B8%EC%84%9C,%20%EC%9D%B4%EB%B2%A4%ED%8A%B8,%20%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4/01-%EB%AC%B8%EC%84%9C/09-%EC%9A%94%EC%86%8C%20%EC%82%AC%EC%9D%B4%EC%A6%88%EC%99%80%20%EC%8A%A4%ED%81%AC%EB%A1%A4.md#%EC%9A%94%EC%86%8C-%EC%82%AC%EC%9D%B4%EC%A6%88%EC%99%80-%EC%8A%A4%ED%81%AC%EB%A1%A4)
- 기하 프로퍼티
- offsetParent와 offsetLeft, offsetTop
- offsetWidth와 offsetHeight
- clientTop과 clientLeft
- clientWidth와 clientHeight
- scrollWidth와 scrollHeight
- scrollLeft와 scrollTop
- CSS에서 너비와 높이를 얻지 말아야 한다.
### 10. [브라우저 창 사이즈와 스크롤](https://github.com/autroshot/studyroom/blob/main/01-javascript/02-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80:%20%EB%AC%B8%EC%84%9C,%20%EC%9D%B4%EB%B2%A4%ED%8A%B8,%20%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4/01-%EB%AC%B8%EC%84%9C/10-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80%20%EC%B0%BD%20%EC%82%AC%EC%9D%B4%EC%A6%88%EC%99%80%20%EC%8A%A4%ED%81%AC%EB%A1%A4.md#%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80-%EC%B0%BD-%EC%82%AC%EC%9D%B4%EC%A6%88%EC%99%80-%EC%8A%A4%ED%81%AC%EB%A1%A4)
- 브라우저 창의 너비와 높이
- 문서의 너비와 높이
- 스크롤 정보 얻기
- scrollTo, scrollBy로 스크롤 상태 변경하기
- scrollIntoView
- 스크롤 막기
### 11. [좌표](https://github.com/autroshot/studyroom/blob/main/01-javascript/02-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80:%20%EB%AC%B8%EC%84%9C,%20%EC%9D%B4%EB%B2%A4%ED%8A%B8,%20%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4/01-%EB%AC%B8%EC%84%9C/11-%EC%A2%8C%ED%91%9C.md#%EC%A2%8C%ED%91%9C)
- getBoundingClientRect로 요소 좌표 얻기
- elementFromPoint(x, y)
- 요소를 창 내 특정 좌표에 고정하기
- 문서 기준 좌표

## [02] 이벤트 기초
### 1. []()
### 2. []()
### 3. []()
### 4. []()
### 5. []()
