# REST

REST(Representational State Transfer)는 API 작동 방식에 대한 조건을 부과하는 소프트웨어 아키텍처이다.

REST 아키텍처 스타일을 따르는 API를 REST API라고 한다. REST 아키텍처를 구현하는 웹 서비스를 RESTful 웹 서비스라고 한다. RESTful API라는 용어는 일반적으로 RESTful 웹 API를 나타낸다. REST API와 RESTful API라는 용어는 같은 의미로 사용할 수 있다.

## 구성

- **자원(RESOURCE)** - URI
- **행위(Verb)** - HTTP 메서드
- **표현(Representations)**

## 특징

- 균일한 인터페이스 - 서버가 표준 형식으로 정보를 전송한다.

- 무상태 - 상태정보를 따로 저장하고 관리하지 않는다. 세션 정보나 쿠키정보를 별도로 저장하고 관리하지 않기 때문에 API 서버는 들어오는 요청만을 단순히 처리하면 된다. 때문에 서비스의 자유도가 높아지고 서버에서 불필요한 정보를 관리하지 않음으로써 구현이 단순해진다.

- 캐시 가능 - HTTP라는 기존 웹표준을 그대로 사용하기 때문에 HTTP가 가진 캐싱 기능이 적용 가능하다.
- 자기 표현적 - 자기 표현적이므로 REST API 메시지만 보고도 이를 쉽게 이해 할 수 있다.

- 클라이언트 서버 구조 - REST 서버는 API 제공, 클라이언트는 사용자 인증이나 컨텍스트(세션, 로그인 정보)등을 직접 관리하는 구조로 각각의 역할이 확실히 구분되기 때문에 클라이언트와 서버에서 개발해야 할 내용이 명확해지고 서로간 의존성이 줄어들게 된다.

- 계층형 구조 - REST 서버는 다중 계층으로 구성될 수 있으며 보안, 로드 밸런싱, 암호화 계층을 추가해 구조상의 유연성을 둘 수 있고 프록시, 게이트웨이 같은 네트워크 기반의 중간매체를 사용할 수 있게 한다.

## 디자인 가이드

1. URI는 정보의 자원을 표현해야 한다.
2. 자원에 대한 행위는 HTTP 메서드(GET, POST, PUT, DELETE)로 표현한다.

### REST API 규칙

URI는 정보의 자원을 표현해야 하며 리소스명은 동사보다는 명사를 사용한다.

```
GET /members/delete/1
```

위와 같은 방식은 REST를 제대로 적용하지 않은 URI이다. URI는 자원을 표현하는데 중점을 두어야 합니다. `delete`와 같은 행위에 대한 표현이 들어가서는 안된다.

**자원에 대한 행위는 HTTP 메서드로 표현한다.**

위의 잘못 된 URI를 HTTP 메서드를 통해 수정해 보면 다음과 같다.

```
DELETE /members/1
```

참고로 HTTP 메서드는 다음과 같다.

| METHOD | 역할               |
| :----- | :----------------- |
| POST   | 리소스를 생성한다. |
| GET    | 리소스를 조회한다. |
| PUT    | 리소스를 수정한다. |
| DELETE | 리소스를 삭제한다. |

GET 메서드에서 URI에 따라 조회 내용이 달라질 수 있다. 예를 들어 `/members`는 전체 회원의 목록을 조회하고 `/members/1`는 회원 한 명의 정보를 조회한다.

다음은 HTTP 메서드를 사용한 작업 정의 예시이다.

| **리소스**          | **POST**                     | **GET**                      | **PUT**                                        | **DELETE**              |
| :------------------ | :--------------------------- | :--------------------------- | :--------------------------------------------- | :---------------------- |
| /customers          | 새 고객 만들기               | 모든 고객 검색               | 고객 대량 업데이트                             | 모든 고객 제거          |
| /customers/1        | Error                        | 고객 1에 대한 세부 정보 검색 | 고객 1이 있는 경우 고객 1의 세부 정보 업데이트 | 고객 1 제거             |
| /customers/1/orders | 고객 1에 대한 새 주문 만들기 | 고객 1에 대한 모든 주문 검색 | 고객 1의 주문 대량 업데이트                    | 고객 1의 모든 주문 제거 |

### URI 설계 규칙

- `/`는 계층 관계를 나타내는 데 사용한다.

```
http://restapi.example.com/houses/apartments
http://restapi.example.com/animals/mammals/whales
```

- 마지막에 `/`를 넣지 않는다.

```
http://restapi.example.com/houses/apartments/ (X)
http://restapi.example.com/houses/apartments  (0)
```

- `_` 대신 `-`을 사용한다.

- 소문자만 사용한다.

- 파일 확장자를 넣지 않는다.

### 리소스 간의 관계 표현하기

일반적으로 소유(has) 관계를 표현할 때 `/리소스명/리소스 ID/관계가 있는 다른 리소스명`을 이용한다.

```
GET : /users/{userid}/devices
```

### 도큐먼트와 컬렉션

도큐먼트는 객체, 컬렉션은 객체들의 집합으로 이해할 수 있다.

예시:

```
http://restapi.example.com/sports/soccer/players/13
```

`sports`와 `players`는 컬렉션을, `soccer`와 `13`은 도큐먼트를 표현한다.

컬렉션은 복수형을 사용한다는 점을 주목해야 한다.

> **참조**
>
> - https://meetup.toast.com/posts/92
> - https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html
> - https://docs.microsoft.com/ko-kr/azure/architecture/best-practices/api-design