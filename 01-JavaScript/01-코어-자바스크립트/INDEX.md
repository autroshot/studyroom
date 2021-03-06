# 코어 자바스크립트
호스트 환경에 종속되지 않는 코어 자바스크립트에 대해 학습한다.
## [01] [소개](./01-소개.md)
### 1. 자바스크립트란?
### 2. 매뉴얼과 명세서
### 3. 코드 에디터

## [02] 자바스크립트 기본
자바스크립트의 기본 문법에 대해 알아본다.
### 1. [Hello, world!](./02-자바스크립트-기본/01-Hello-world.md)
### 2. [코드 구조](./02-자바스크립트-기본/02-코드-구조.md)
- 문
- 주석
### 3. [엄격 모드](./02-자바스크립트-기본/03-엄격-모드.md)
### 4. [변수와 상수](./02-자바스크립트-기본/04-변수와-상수.md)
### 5. [자료형](./02-자바스크립트-기본/05-자료형.md)
- 숫자형
- BigInt
- 문자형
- 불린형
- null
- undefined
- 객체형
- 심볼형
- typeof 연산자
### 6. [alert, prompt, confirm을 이용한 상호작용](./02-자바스크립트-기본/06-alert-prompt-confirm을-이용한-상호작용.md)
### 7. [형 변환](./02-자바스크립트-기본/07-형-변환.md)
### 8. [기본 연산자와 수학](./02-자바스크립트-기본/08-기본-연산자와-수학.md)
- 이항 연산자 '+'의 문자열 연결
- 단항 연산자 '+'의 형 변환
- 연산자 우선순위
- 할당 연산자 '='
- 복합 할당 연산자
- 증가·감소 연산자
- 비트 연산자
- 쉼표 연산자
### 9. [비교 연산자](./02-자바스크립트-기본/09-비교-연산자.md)
- 불린형 반환
- 문자열 비교
- 다른 형을 가진 값의 비교
- 일치 연산자
- null/undefined에 적용되는 특수한 규칙
### 10. [if와 ‘?’를 사용한 조건 처리](./02-자바스크립트-기본/10-if와-조건부-연산자를-사용한-조건-처리.md)
- 'if’문
- 조건부 연산자 ‘?’
- 부적절한 ‘?’
### 11. [논리 연산자](./02-자바스크립트-기본/11-논리-연산자.md)
- || (OR)
- && (AND)
- ! (NOT)
### 12. [nullish 병합 연산자 ‘??’](./02-자바스크립트-기본/12-nullish-병합-연산자.md)
### 13. [while과 for 반복문](./02-자바스크립트-기본/13-while과-for-반복문.md)
- ‘while’ 반복문
- ‘do...while’ 반복문
- ‘for’ 반복문
- break/continue와 레이블
### 14. [switch문](./02-자바스크립트-기본/14-switch문.md)
### 15. [함수](./02-자바스크립트-기본/15-함수.md)
- 함수 선언
- 지역 변수와 전역 변수
- 매개 변수
- 기본값
- 반환 값
- 함수 이름짓기
- 함수 == 주석
### 16. [함수 표현식](./02-자바스크립트-기본/16-함수-표현식.md)
- 콜백 함수
- 익명 함수
- 함수 표현식 vs 함수 선언문
### 17. [화살표 함수 기본](./02-자바스크립트-기본/17-화살표-함수-기본.md)
### 18. [요약 링크](https://ko.javascript.info/javascript-specials)

## [03] 코드 품질
코딩 관례(coding practice)에 대해 알아본다.
### 1. [Chrome으로 디버깅하기](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/03-%EC%BD%94%EB%93%9C%20%ED%92%88%EC%A7%88/01-Chrome%EC%9C%BC%EB%A1%9C%20%EB%94%94%EB%B2%84%EA%B9%85%ED%95%98%EA%B8%B0.md#chrome%EC%9C%BC%EB%A1%9C-%EB%94%94%EB%B2%84%EA%B9%85%ED%95%98%EA%B8%B0)
### 2. [코딩 스타일](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/03-%EC%BD%94%EB%93%9C%20%ED%92%88%EC%A7%88/02-%EC%BD%94%EB%94%A9%20%EC%8A%A4%ED%83%80%EC%9D%BC.md#%EC%BD%94%EB%94%A9-%EC%8A%A4%ED%83%80%EC%9D%BC)
- 중괄호
- 가로 길이
- 들여쓰기
- 세미콜론
- 중첩 레벨
- 함수의 위치
- 스타일 가이드
- Linter
### 3. [주석](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/03-%EC%BD%94%EB%93%9C%20%ED%92%88%EC%A7%88/03-%EC%A3%BC%EC%84%9D.md#%EC%A3%BC%EC%84%9D)
- 좋지 않은 주석
- 좋은 주석
### 4. [닌자 코드](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/03-%EC%BD%94%EB%93%9C%20%ED%92%88%EC%A7%88/04-%EB%8B%8C%EC%9E%90%20%EC%BD%94%EB%93%9C.md#%EB%8B%8C%EC%9E%90-%EC%BD%94%EB%93%9C)
### 5. [테스트 자동화와 Mocha](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/03-%EC%BD%94%EB%93%9C%20%ED%92%88%EC%A7%88/05-%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%EC%99%80%20Mocha.md#%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%9E%90%EB%8F%99%ED%99%94%EC%99%80-mocha)
### 6. [폴리필](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/03-%EC%BD%94%EB%93%9C%20%ED%92%88%EC%A7%88/06-%ED%8F%B4%EB%A6%AC%ED%95%84.md#%ED%8F%B4%EB%A6%AC%ED%95%84)
- 바벨

## [04] 객체: 기본
### 1. [객체](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/04-%EA%B0%9D%EC%B2%B4:%20%EA%B8%B0%EB%B3%B8/01-%EA%B0%9D%EC%B2%B4.md#%EA%B0%9D%EC%B2%B4)
- 리터럴과 프로퍼티
- 대괄호 표기법
- 단축 프로퍼티
- 프로퍼티 이름의 제약사항
- ‘in’ 연산자로 프로퍼티 존재 여부 확인하기
- 'for...in' 반복문
### 2. [참조에 의한 객체 복사](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/04-%EA%B0%9D%EC%B2%B4:%20%EA%B8%B0%EB%B3%B8/02-%EC%B0%B8%EC%A1%B0%EC%97%90%20%EC%9D%98%ED%95%9C%20%EA%B0%9D%EC%B2%B4%20%EB%B3%B5%EC%82%AC.md#%EC%B0%B8%EC%A1%B0%EC%97%90-%EC%9D%98%ED%95%9C-%EA%B0%9D%EC%B2%B4-%EB%B3%B5%EC%82%AC)
- 객체 복사, 병합과 Object.assign
- 중첩 객체 복사
### 3. [가비지 컬렉션](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/04-%EA%B0%9D%EC%B2%B4:%20%EA%B8%B0%EB%B3%B8/03-%EA%B0%80%EB%B9%84%EC%A7%80%20%EC%BB%AC%EB%A0%89%EC%85%98.md#%EA%B0%80%EB%B9%84%EC%A7%80-%EC%BB%AC%EB%A0%89%EC%85%98)
### 4. [메서드와 this](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/04-%EA%B0%9D%EC%B2%B4:%20%EA%B8%B0%EB%B3%B8/04-%EB%A9%94%EC%84%9C%EB%93%9C%EC%99%80%20this.md#%EB%A9%94%EC%84%9C%EB%93%9C%EC%99%80-this)
- 메서드 만들기
- 메서드와 this
- 자유로운 this
- this가 없는 화살표 함수
### 5. [new 연산자와 생성자 함수](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/04-%EA%B0%9D%EC%B2%B4:%20%EA%B8%B0%EB%B3%B8/05-new%20%EC%97%B0%EC%82%B0%EC%9E%90%EC%99%80%20%EC%83%9D%EC%84%B1%EC%9E%90%20%ED%95%A8%EC%88%98.md#new-%EC%97%B0%EC%82%B0%EC%9E%90%EC%99%80-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)
- 생성자 함수
- new.target과 생성자 함수
- 생성자와 return문
- 생성자 내 메서드
### 6. [옵셔널 체이닝 '?.'](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/04-%EA%B0%9D%EC%B2%B4:%20%EA%B8%B0%EB%B3%B8/06-%EC%98%B5%EC%85%94%EB%84%90%20%EC%B2%B4%EC%9D%B4%EB%8B%9D%20'%3F.'.md#%EC%98%B5%EC%85%94%EB%84%90-%EC%B2%B4%EC%9D%B4%EB%8B%9D-)
- 옵셔널 체이닝이 필요한 이유
- 옵셔널 체이닝의 등장
- 단락 평가
- ?.()와 ?.[]
### 7. [심볼형](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/04-%EA%B0%9D%EC%B2%B4:%20%EA%B8%B0%EB%B3%B8/07-%EC%8B%AC%EB%B3%BC%ED%98%95.md#%EC%8B%AC%EB%B3%BC%ED%98%95)
- 심볼
- '숨김' 프로퍼티
- 전역 심볼
- 시스템 심볼
### 8. [객체를 원시형으로 변환하기](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/04-%EA%B0%9D%EC%B2%B4:%20%EA%B8%B0%EB%B3%B8/08-%EA%B0%9D%EC%B2%B4%EB%A5%BC%20%EC%9B%90%EC%8B%9C%ED%98%95%EC%9C%BC%EB%A1%9C%20%EB%B3%80%ED%99%98%ED%95%98%EA%B8%B0.md#%EA%B0%9D%EC%B2%B4%EB%A5%BC-%EC%9B%90%EC%8B%9C%ED%98%95%EC%9C%BC%EB%A1%9C-%EB%B3%80%ED%99%98%ED%95%98%EA%B8%B0)
- ToPrimitive
- Symbol.toPrimitive
- toString과 valueOf
- 반환 타입
- 추가 형 변환

## [05] 자료구조와 자료형
### 1. [원시값의 메서드](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/05-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80%20%EC%9E%90%EB%A3%8C%ED%98%95/01-%EC%9B%90%EC%8B%9C%EA%B0%92%EC%9D%98%20%EB%A9%94%EC%84%9C%EB%93%9C.md#%EC%9B%90%EC%8B%9C%EA%B0%92%EC%9D%98-%EB%A9%94%EC%84%9C%EB%93%9C)
### 2. [숫자형](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/05-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80%20%EC%9E%90%EB%A3%8C%ED%98%95/02-%EC%88%AB%EC%9E%90%ED%98%95.md#%EC%88%AB%EC%9E%90%ED%98%95)
- 숫자를 입력하는 다양한 방법
- toString(base)
- 어림수 구하기
- 부정확한 계산
- isNaN과 isFinite
- parseInt와 parseFloat
- 기타 수학 메서드
### 3. [문자열](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/05-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80%20%EC%9E%90%EB%A3%8C%ED%98%95/03-%EB%AC%B8%EC%9E%90%EC%97%B4.md#%EB%AC%B8%EC%9E%90%EC%97%B4)
- 따옴표
- 특수 기호
- 문자열의 길이
- 특정 글자에 접근하기
- 문자열의 불변성
- 대·소문자 변경하기
- 부분 문자열 찾기
- 부분 문자열 추출하기
- 문자열 비교하기
- 기타 문자열 메서드
- 문자열 심화
### 4. [배열](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/05-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80%20%EC%9E%90%EB%A3%8C%ED%98%95/04-%EB%B0%B0%EC%97%B4.md#%EB%B0%B0%EC%97%B4)
- 배열 선언
- pop/push와 shift/unshift
- 배열의 내부 동작 원리
- 성능
- 반복문
- length 프로퍼티
- new Array()
- 다차원 배열
- toString
### 5. [배열과 메서드](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/05-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80%20%EC%9E%90%EB%A3%8C%ED%98%95/05-%EB%B0%B0%EC%97%B4%EA%B3%BC%20%EB%A9%94%EC%84%9C%EB%93%9C.md#%EB%B0%B0%EC%97%B4%EA%B3%BC-%EB%A9%94%EC%84%9C%EB%93%9C)
- 요소 추가·제거 메서드
- forEach로 반복작업 하기
- 배열 탐색하기
- 배열을 변형하는 메서드
- Array.isArray로 배열 여부 알아내기
- 배열 메서드와 'thisArg'
- 기타 배열 메서드
### 6. [iterable 객체](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/05-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80%20%EC%9E%90%EB%A3%8C%ED%98%95/06-iterable%20%EA%B0%9D%EC%B2%B4.md#iterable-%EA%B0%9D%EC%B2%B4)
- Symbol.iterator
- 문자열은 이터러블이다
- 이터레이터를 명시적으로 호출하기
- 이터러블과 유사 배열
- Array.from
### 7. [맵과 셋](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/05-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80%20%EC%9E%90%EB%A3%8C%ED%98%95/07-%EB%A7%B5%EA%B3%BC%20%EC%85%8B.md#%EB%A7%B5%EA%B3%BC-%EC%85%8B)
- 맵
- 맵의 요소에 반복 작업하기
- Object.entries: 객체를 맵으로 바꾸기
- Object.fromEntries: 맵을 객체로 바꾸기
- 셋
- 셋의 값에 반복 작업하기
### 8. [위크맵과 위크셋](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/05-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80%20%EC%9E%90%EB%A3%8C%ED%98%95/08-%EC%9C%84%ED%81%AC%EB%A7%B5%EA%B3%BC%20%EC%9C%84%ED%81%AC%EC%85%8B.md#%EC%9C%84%ED%81%AC%EB%A7%B5%EA%B3%BC-%EC%9C%84%ED%81%AC%EC%85%8B)
- 위크맵
- 유스 케이스: 추가 데이터
- 유스 케이스: 캐싱
- 위크셋
### 9. [Object.keys, values, entries](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/05-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80%20%EC%9E%90%EB%A3%8C%ED%98%95/09-Object.keys%2C%20values%2C%20entries.md#objectkeys-values-entries)
- Object.keys, values, entries
- 객체 변환하기
### 10. [구조 분해 할당](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/05-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80%20%EC%9E%90%EB%A3%8C%ED%98%95/10-%EA%B5%AC%EC%A1%B0%20%EB%B6%84%ED%95%B4%20%ED%95%A0%EB%8B%B9.md#%EA%B5%AC%EC%A1%B0-%EB%B6%84%ED%95%B4-%ED%95%A0%EB%8B%B9)
- 배열 분해하기
- 객체 분해하기
- 중첩 구조 분해
- 똑똑한 함수 매개변수
### 11. [Date 객체와 날짜](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/05-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80%20%EC%9E%90%EB%A3%8C%ED%98%95/11-Date%20%EA%B0%9D%EC%B2%B4%EC%99%80%20%EB%82%A0%EC%A7%9C.md#date-%EA%B0%9D%EC%B2%B4%EC%99%80-%EB%82%A0%EC%A7%9C)
- 객체 생성하기
- 날짜 구성요소 얻기
- 날짜 구성요소 설정하기
- 자동 고침
- Date 객체를 숫자로 변경해 시간차 측정하기
- Date.now()
- 벤치마크 테스트
- Date.parse와 문자열
### 12. [JSON과 메서드](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/05-%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0%EC%99%80%20%EC%9E%90%EB%A3%8C%ED%98%95/12-JSON%EA%B3%BC%20%EB%A9%94%EC%84%9C%EB%93%9C.md#json%EA%B3%BC-%EB%A9%94%EC%84%9C%EB%93%9C)
- JSON.stringify
- replacer로 원하는 프로퍼티만 직렬화하기
- space로 가독성 높이기
- 커스텀 "toJSON"
- JSON.parse
- reviver 사용하기

## [06] 함수 심화학습
### 1. [재귀와 스택](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/06-%ED%95%A8%EC%88%98%20%EC%8B%AC%ED%99%94%ED%95%99%EC%8A%B5/01-%EC%9E%AC%EA%B7%80%EC%99%80%20%EC%8A%A4%ED%83%9D.md#%EC%9E%AC%EA%B7%80%EC%99%80-%EC%8A%A4%ED%83%9D)
### 2. [나머지 매개변수와 스프레드 문법](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/06-%ED%95%A8%EC%88%98%20%EC%8B%AC%ED%99%94%ED%95%99%EC%8A%B5/02-%EB%82%98%EB%A8%B8%EC%A7%80%20%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98%EC%99%80%20%EC%8A%A4%ED%94%84%EB%A0%88%EB%93%9C%20%EB%AC%B8%EB%B2%95.md#%EB%82%98%EB%A8%B8%EC%A7%80-%EB%A7%A4%EA%B0%9C%EB%B3%80%EC%88%98%EC%99%80-%EC%8A%A4%ED%94%84%EB%A0%88%EB%93%9C-%EB%AC%B8%EB%B2%95)
- 나머지 매개변수 ...
- arguments 객체
- 스프레드 문법
- 배열과 객체의 복사본 만들기
### 3. [변수의 스코프와 클로저](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/06-%ED%95%A8%EC%88%98%20%EC%8B%AC%ED%99%94%ED%95%99%EC%8A%B5/03-%EB%B3%80%EC%88%98%EC%9D%98%20%EC%9C%A0%ED%9A%A8%EB%B2%94%EC%9C%84%EC%99%80%20%ED%81%B4%EB%A1%9C%EC%A0%80.md#%EB%B3%80%EC%88%98%EC%9D%98-%EC%8A%A4%EC%BD%94%ED%94%84%EC%99%80-%ED%81%B4%EB%A1%9C%EC%A0%80)
- 코드 블록
- 중첩 함수
- 렉시컬 환경
- 가비지 컬렉션
### 4. [오래된 var](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/06-%ED%95%A8%EC%88%98%20%EC%8B%AC%ED%99%94%ED%95%99%EC%8A%B5/04-%EC%98%A4%EB%9E%98%EB%90%9C%20var.md#%EC%98%A4%EB%9E%98%EB%90%9C-var)
- var는 블록 스코프가 없다
- var는 재선언을 허용한다
- var는 선언하기 전에 사용이 가능하다
### 5. [전역 객체](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/06-%ED%95%A8%EC%88%98%20%EC%8B%AC%ED%99%94%ED%95%99%EC%8A%B5/05-%EC%A0%84%EC%97%AD%20%EA%B0%9D%EC%B2%B4.md#%EC%A0%84%EC%97%AD-%EA%B0%9D%EC%B2%B4)
### 6. [객체로서의 함수와 기명 함수 표현식](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/06-%ED%95%A8%EC%88%98%20%EC%8B%AC%ED%99%94%ED%95%99%EC%8A%B5/06-%EA%B0%9D%EC%B2%B4%EB%A1%9C%EC%84%9C%EC%9D%98%20%ED%95%A8%EC%88%98%EC%99%80%20%EA%B8%B0%EB%AA%85%20%ED%95%A8%EC%88%98%20%ED%91%9C%ED%98%84%EC%8B%9D.md#%EA%B0%9D%EC%B2%B4%EB%A1%9C%EC%84%9C%EC%9D%98-%ED%95%A8%EC%88%98%EC%99%80-%EA%B8%B0%EB%AA%85-%ED%95%A8%EC%88%98-%ED%91%9C%ED%98%84%EC%8B%9D)
- ‘name’ 프로퍼티
- ‘length’ 프로퍼티
- 커스텀 프로퍼티
- 기명 함수 표현식
### 7. [new Function 문법](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/06-%ED%95%A8%EC%88%98%20%EC%8B%AC%ED%99%94%ED%95%99%EC%8A%B5/07-new%20Function%20%EB%AC%B8%EB%B2%95.md#new-function-%EB%AC%B8%EB%B2%95)
### 8. [setTimeout과 setInterval을 이용한 호출 스케줄링](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/06-%ED%95%A8%EC%88%98%20%EC%8B%AC%ED%99%94%ED%95%99%EC%8A%B5/08-setTimeout%EA%B3%BC%20setInterval%EC%9D%84%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%ED%98%B8%EC%B6%9C%20%EC%8A%A4%EC%BC%80%EC%A4%84%EB%A7%81.md#settimeout%EA%B3%BC-setinterval%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%ED%98%B8%EC%B6%9C-%EC%8A%A4%EC%BC%80%EC%A4%84%EB%A7%81)
- setTimeout
- setInterval
- 중첩 setTimeout
- 대기 시간이 0인 setTimeout
### 9. [call/apply와 데코레이터, 포워딩](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/06-%ED%95%A8%EC%88%98%20%EC%8B%AC%ED%99%94%ED%95%99%EC%8A%B5/09-call%C2%B7apply%EC%99%80%20%EB%8D%B0%EC%BD%94%EB%A0%88%EC%9D%B4%ED%84%B0%2C%20%ED%8F%AC%EC%9B%8C%EB%94%A9.md#callapply%EC%99%80-%EB%8D%B0%EC%BD%94%EB%A0%88%EC%9D%B4%ED%84%B0-%ED%8F%AC%EC%9B%8C%EB%94%A9)
- 코드 변경 없이 캐싱 기능 추가하기
- 'func.call’를 사용해 컨텍스트 지정하기
- 여러 인수 전달하기
- func.apply
- 메서드 빌리기
- 데코레이터와 함수 프로퍼티
### 10. [함수 바인딩](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/06-%ED%95%A8%EC%88%98%20%EC%8B%AC%ED%99%94%ED%95%99%EC%8A%B5/10-%ED%95%A8%EC%88%98%20%EB%B0%94%EC%9D%B8%EB%94%A9.md#%ED%95%A8%EC%88%98-%EB%B0%94%EC%9D%B8%EB%94%A9)
### 11. [화살표 함수 다시 살펴보기](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/06-%ED%95%A8%EC%88%98%20%EC%8B%AC%ED%99%94%ED%95%99%EC%8A%B5/11-%ED%99%94%EC%82%B4%ED%91%9C%20%ED%95%A8%EC%88%98%20%EB%8B%A4%EC%8B%9C%20%EC%82%B4%ED%8E%B4%EB%B3%B4%EA%B8%B0.md#%ED%99%94%EC%82%B4%ED%91%9C-%ED%95%A8%EC%88%98-%EB%8B%A4%EC%8B%9C-%EC%82%B4%ED%8E%B4%EB%B3%B4%EA%B8%B0)
- 화살표 함수에는 'this’가 없다.
- 화살표 함수에는 'arguments’가 없다.

## [07] 객체 프로퍼티 설정
### 1. [프로퍼티 플래그와 설명자](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/07-%EA%B0%9D%EC%B2%B4%20%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%20%EC%84%A4%EC%A0%95/01-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%20%ED%94%8C%EB%9E%98%EA%B7%B8%EC%99%80%20%EC%84%A4%EB%AA%85%EC%9E%90.md#%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0-%ED%94%8C%EB%9E%98%EA%B7%B8%EC%99%80-%EC%84%A4%EB%AA%85%EC%9E%90)
- 프로퍼티 플래그
- writable 플래그
- enumerable 플래그
- configurable 플래그
- Object.defineProperties
- Object.getOwnPropertyDescriptors
- 객체 수정을 막아주는 다양한 메서드
### 2. [프로퍼티 getter와 setter](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/07-%EA%B0%9D%EC%B2%B4%20%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%20%EC%84%A4%EC%A0%95/02-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%20getter%EC%99%80%20setter.md#%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0-getter%EC%99%80-setter)

## [08] 프로토타입과 프로토타입 상속
### 1. [프로토타입 상속](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/08-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85%EA%B3%BC%20%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85%20%EC%83%81%EC%86%8D/01-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85%20%EC%83%81%EC%86%8D.md#%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%EC%83%81%EC%86%8D)
- [[Prototype]]
- 프로토타입은 읽기 전용이다
- this가 나타내는 것
- for…in 반복문
### 2. [함수의 prototype 프로퍼티](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/08-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85%EA%B3%BC%20%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85%20%EC%83%81%EC%86%8D/02-%ED%95%A8%EC%88%98%EC%9D%98%20prototype%20%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0.md#%ED%95%A8%EC%88%98%EC%9D%98-prototype-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0)
- 함수의 디폴트 프로퍼티 prototype과 constructor 프로퍼티
### 3. [내장 객체의 프로토타입](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/08-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85%EA%B3%BC%20%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85%20%EC%83%81%EC%86%8D/03-%EB%82%B4%EC%9E%A5%20%EA%B0%9D%EC%B2%B4%EC%9D%98%20%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85.md#%EB%82%B4%EC%9E%A5-%EA%B0%9D%EC%B2%B4%EC%9D%98-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85)
- Object.prototype
- 다양한 내장 객체의 프로토타입
- 원시값
- 네이티브 프로토타입 변경하기
- 프로토타입에서 메서드 빌려오기
### 4. [프로토타입 메서드와 __proto__가 없는 객체](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/08-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85%EA%B3%BC%20%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85%20%EC%83%81%EC%86%8D/04-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85%20%EB%A9%94%EC%84%9C%EB%93%9C%EC%99%80%20__proto__%EA%B0%80%20%EC%97%86%EB%8A%94%20%EA%B0%9D%EC%B2%B4.md#%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-%EB%A9%94%EC%84%9C%EB%93%9C%EC%99%80-__proto__%EA%B0%80-%EC%97%86%EB%8A%94-%EA%B0%9D%EC%B2%B4)

## [09] 클래스
### 1. [클래스와 기본 문법](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/09-%ED%81%B4%EB%9E%98%EC%8A%A4/01-%ED%81%B4%EB%9E%98%EC%8A%A4%EC%99%80%20%EA%B8%B0%EB%B3%B8%20%EB%AC%B8%EB%B2%95.md#%ED%81%B4%EB%9E%98%EC%8A%A4%EC%99%80-%EA%B8%B0%EB%B3%B8-%EB%AC%B8%EB%B2%95)
- 기본 문법
- 클래스란?
- 클래스는 단순한 편의 문법이 아니다
- 클래스 표현식
- getter와 setter
- 계산된 메서드 이름 […]
- 클래스 필드
### 2. [클래스 상속](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/09-%ED%81%B4%EB%9E%98%EC%8A%A4/02-%ED%81%B4%EB%9E%98%EC%8A%A4%20%EC%83%81%EC%86%8D.md#%ED%81%B4%EB%9E%98%EC%8A%A4-%EC%83%81%EC%86%8D)
- ‘extends’ 키워드
- 메서드 오버라이딩
- 생성자 오버라이딩
- super 키워드와 [[HomeObject]]
### 3. [정적 메서드와 정적 프로퍼티](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/09-%ED%81%B4%EB%9E%98%EC%8A%A4/03-%EC%A0%95%EC%A0%81%20%EB%A9%94%EC%84%9C%EB%93%9C%EC%99%80%20%EC%A0%95%EC%A0%81%20%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0.md#%EC%A0%95%EC%A0%81-%EB%A9%94%EC%84%9C%EB%93%9C%EC%99%80-%EC%A0%95%EC%A0%81-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0)
### 4. [private, protected 프로퍼티와 메서드](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/09-%ED%81%B4%EB%9E%98%EC%8A%A4/04-private%2C%20protected%20%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%EC%99%80%20%EB%A9%94%EC%84%9C%EB%93%9C.md#private-protected-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%EC%99%80-%EB%A9%94%EC%84%9C%EB%93%9C)
### 5. [내장 클래스 확장하기](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/09-%ED%81%B4%EB%9E%98%EC%8A%A4/05-%EB%82%B4%EC%9E%A5%20%ED%81%B4%EB%9E%98%EC%8A%A4%20%ED%99%95%EC%9E%A5%ED%95%98%EA%B8%B0.md#%EB%82%B4%EC%9E%A5-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%99%95%EC%9E%A5%ED%95%98%EA%B8%B0)
### 6. ['instanceof'로 클래스 확인하기](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/09-%ED%81%B4%EB%9E%98%EC%8A%A4/06-'instanceof'%EB%A1%9C%20%ED%81%B4%EB%9E%98%EC%8A%A4%20%ED%99%95%EC%9D%B8%ED%95%98%EA%B8%B0.md#instanceof%EB%A1%9C-%ED%81%B4%EB%9E%98%EC%8A%A4-%ED%99%95%EC%9D%B8%ED%95%98%EA%B8%B0)
- 보너스: 타입 확인을 위한 Object.prototype.toString
### 7. [믹스인](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/09-%ED%81%B4%EB%9E%98%EC%8A%A4/07-%EB%AF%B9%EC%8A%A4%EC%9D%B8.md#%EB%AF%B9%EC%8A%A4%EC%9D%B8)

## [10] 에러 핸들링
### 1. ['try..catch'와 에러 핸들링](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/10-%EC%97%90%EB%9F%AC%20%ED%95%B8%EB%93%A4%EB%A7%81/01-'try..catch'%EC%99%80%20%EC%97%90%EB%9F%AC%20%ED%95%B8%EB%93%A4%EB%A7%81.md#trycatch%EC%99%80-%EC%97%90%EB%9F%AC-%ED%95%B8%EB%93%A4%EB%A7%81)
- ‘try…catch’ 문법
- 에러 객체
- 선택적 ‘catch’ 바인딩
- ‘try…catch’ 사용하기
- 직접 에러를 만들어서 던지기
- 에러 다시 던지기
- try…catch…finally
- 전역 catch
### 2. [커스텀 에러와 에러 확장](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/10-%EC%97%90%EB%9F%AC%20%ED%95%B8%EB%93%A4%EB%A7%81/02-%EC%BB%A4%EC%8A%A4%ED%85%80%20%EC%97%90%EB%9F%AC%EC%99%80%20%EC%97%90%EB%9F%AC%20%ED%99%95%EC%9E%A5.md#%EC%BB%A4%EC%8A%A4%ED%85%80-%EC%97%90%EB%9F%AC%EC%99%80-%EC%97%90%EB%9F%AC-%ED%99%95%EC%9E%A5)
- 에러 확장하기
- 더 깊게 상속하기
- 예외 감싸기

## [11] 프라미스와 async, await
### 1. [콜백](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/11-%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4%EC%99%80%20async%2C%20await/01-%EC%BD%9C%EB%B0%B1.md#%EC%BD%9C%EB%B0%B1)
### 2. [프라미스](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/11-%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4%EC%99%80%20async%2C%20await/02-%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4.md#%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4)
### 3. [프라미스 체이닝](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/11-%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4%EC%99%80%20async%2C%20await/03-%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4%20%EC%B2%B4%EC%9D%B4%EB%8B%9D.md#%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4-%EC%B2%B4%EC%9D%B4%EB%8B%9D)
- 프라미스 반환하기
- fetch와 체이닝 함께 응용하기
### 4. [프라미스와 에러 핸들링](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/11-%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4%EC%99%80%20async%2C%20await/04-%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4%EC%99%80%20%EC%97%90%EB%9F%AC%20%ED%95%B8%EB%93%A4%EB%A7%81.md#%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4%EC%99%80-%EC%97%90%EB%9F%AC-%ED%95%B8%EB%93%A4%EB%A7%81)
- 암시적 try…catch
- 다시 던지기
- 처리되지 못한 거부
### 5. [프라미스 API](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/11-%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4%EC%99%80%20async%2C%20await/05-%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4%20API.md#%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4-api)
- Promise.all
- Promise.allSettled
- Promise.race
- Promise.resolve와 Promise.reject
### 6. [프라미스화](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/11-%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4%EC%99%80%20async%2C%20await/06-%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4%ED%99%94.md#%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4%ED%99%94)
### 7. [마이크로태스크](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/11-%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4%EC%99%80%20async%2C%20await/07-%EB%A7%88%EC%9D%B4%ED%81%AC%EB%A1%9C%ED%83%9C%EC%8A%A4%ED%81%AC.md#%EB%A7%88%EC%9D%B4%ED%81%AC%EB%A1%9C%ED%83%9C%EC%8A%A4%ED%81%AC)
### 8. [async와 await](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/11-%ED%94%84%EB%9D%BC%EB%AF%B8%EC%8A%A4%EC%99%80%20async%2C%20await/08-async%EC%99%80%20await.md#async%EC%99%80-await)

## [12] 제너레이터와 비동기 이터레이션
### 1. [제너레이터](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/12-%EC%A0%9C%EB%84%88%EB%A0%88%EC%9D%B4%ED%84%B0%EC%99%80%20%EB%B9%84%EB%8F%99%EA%B8%B0%20%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%EC%85%98/01-%EC%A0%9C%EB%84%88%EB%A0%88%EC%9D%B4%ED%84%B0.md#%EC%A0%9C%EB%84%88%EB%A0%88%EC%9D%B4%ED%84%B0)
### 2. [async 이터레이터와 제너레이터](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/12-%EC%A0%9C%EB%84%88%EB%A0%88%EC%9D%B4%ED%84%B0%EC%99%80%20%EB%B9%84%EB%8F%99%EA%B8%B0%20%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%EC%85%98/02-async%20%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0%EC%99%80%20%EC%A0%9C%EB%84%88%EB%A0%88%EC%9D%B4%ED%84%B0.md#async-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0%EC%99%80-%EC%A0%9C%EB%84%88%EB%A0%88%EC%9D%B4%ED%84%B0)

## [13] 모듈
### 1. [모듈 소개](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/13-%EB%AA%A8%EB%93%88/01-%EB%AA%A8%EB%93%88%20%EC%86%8C%EA%B0%9C.md#%EB%AA%A8%EB%93%88-%EC%86%8C%EA%B0%9C)
- 빌드 툴
### 2. [모듈 내보내고 가져오기](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/13-%EB%AA%A8%EB%93%88/02-%EB%AA%A8%EB%93%88%20%EB%82%B4%EB%B3%B4%EB%82%B4%EA%B3%A0%20%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0.md#%EB%AA%A8%EB%93%88-%EB%82%B4%EB%B3%B4%EB%82%B4%EA%B3%A0-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0)
### 3. [동적으로 모듈 가져오기](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/13-%EB%AA%A8%EB%93%88/03-%EB%8F%99%EC%A0%81%EC%9C%BC%EB%A1%9C%20%EB%AA%A8%EB%93%88%20%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0.md#%EB%8F%99%EC%A0%81%EC%9C%BC%EB%A1%9C-%EB%AA%A8%EB%93%88-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0)

## [14] 기타
### 1. [Proxy와 Reflect](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/14-%EA%B8%B0%ED%83%80/01-Proxy%EC%99%80%20Reflect.md#proxy%EC%99%80-reflect)
### 2. [Eval: 문자열 코드 실행하기](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/14-%EA%B8%B0%ED%83%80/02-Eval:%20%EB%AC%B8%EC%9E%90%EC%97%B4%20%EC%BD%94%EB%93%9C%20%EC%8B%A4%ED%96%89%ED%95%98%EA%B8%B0.md#eval-%EB%AC%B8%EC%9E%90%EC%97%B4-%EC%BD%94%EB%93%9C-%EC%8B%A4%ED%96%89%ED%95%98%EA%B8%B0)
### 3. [커링](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/14-%EA%B8%B0%ED%83%80/03-%EC%BB%A4%EB%A7%81.md#%EC%BB%A4%EB%A7%81)
### 4. [참조 타입](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/14-%EA%B8%B0%ED%83%80/04-%EC%B0%B8%EC%A1%B0%20%ED%83%80%EC%9E%85.md#%EC%B0%B8%EC%A1%B0-%ED%83%80%EC%9E%85)
### 5. [BigInt](https://github.com/autroshot/studyroom/blob/main/01-javascript/01-%EC%BD%94%EC%96%B4%20%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8/14-%EA%B8%B0%ED%83%80/05-BigInt.md#bigint)
