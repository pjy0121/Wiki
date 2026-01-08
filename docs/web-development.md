# 10. 웹 개발 (Web Development)

---

## 1. 웹 기본 개념

### URL 구조

```
https://www.example.com:443/path/to/resource?query=value#fragment
└─┬─┘   └──────┬──────┘└┬┘└──────┬────────┘└────┬────┘└───┬───┘
scheme       host    port     path          query     fragment
```

### ⭕ 쿠키, 세션, 토큰의 차이는?

| 구분 | 저장 위치 | 특징 |
|------|----------|------|
| 쿠키 | 클라이언트 | 작은 데이터, 만료 시간 설정 |
| 세션 | 서버 | 서버 메모리 사용, 세션 ID는 쿠키로 전달 |
| 토큰 (JWT) | 클라이언트 | 서버 상태 없음 (Stateless), 자체 정보 포함 |

---

## 2. HTTP ◑

### HTTP 메서드 ◑

| 메서드 | 용도 | 안전 | 멱등성 |
|--------|------|------|--------|
| GET | 리소스 조회 | O | O |
| POST | 리소스 생성 | X | X |
| PUT | 리소스 전체 수정 | X | O |
| PATCH | 리소스 부분 수정 | X | X |
| DELETE | 리소스 삭제 | X | O |
| OPTIONS | 지원 메서드 확인 | O | O |
| HEAD | 헤더만 조회 | O | O |

**안전(Safe)**: 리소스를 변경하지 않음
**멱등성(Idempotent)**: 여러 번 호출해도 결과 동일

### ⭕ GET과 POST의 차이는? ◑

| 구분 | GET | POST |
|------|-----|------|
| 용도 | 조회 | 생성/수정 |
| 데이터 전송 | URL 쿼리스트링 | Body |
| 캐싱 | 가능 | 불가능 |
| 멱등성 | O | X |
| 북마크 | 가능 | 불가능 |
| 보안 | 낮음 (URL 노출) | 상대적으로 높음 |

### HTTP 상태 코드 ◑

| 범위 | 의미 | 주요 코드 |
|------|------|----------|
| 1xx | 정보 | 100 Continue |
| 2xx | 성공 | 200 OK, 201 Created, 204 No Content |
| 3xx | 리다이렉션 | 301 Moved Permanently, 302 Found, 304 Not Modified |
| 4xx | 클라이언트 오류 | 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found |
| 5xx | 서버 오류 | 500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable |

### ⭕ HTTP와 HTTPS의 차이는? ◑

| 구분 | HTTP | HTTPS |
|------|------|-------|
| 보안 | 암호화 없음 | TLS/SSL 암호화 |
| 포트 | 80 | 443 |
| 성능 | 빠름 | 암호화 오버헤드 |
| 인증서 | 불필요 | SSL 인증서 필요 |

---

## 3. REST API

| 메서드 | 용도 | 예시 |
|--------|------|------|
| GET | 조회 | GET /users/1 |
| POST | 생성 | POST /users |
| PUT | 전체 수정 | PUT /users/1 |
| PATCH | 부분 수정 | PATCH /users/1 |
| DELETE | 삭제 | DELETE /users/1 |

### REST 설계 원칙

- 리소스 중심 URL
- HTTP 메서드 활용
- 상태 없음 (Stateless)
- 일관된 인터페이스

### RESTful API 설계 가이드 ◑

| 원칙 | 좋은 예 | 나쁜 예 |
|------|---------|---------|
| 명사 사용 | /users | /getUsers |
| 복수형 사용 | /users/1 | /user/1 |
| 계층 구조 | /users/1/posts | /getUserPosts?id=1 |
| 소문자 사용 | /user-profile | /userProfile |

---

## 4. 실시간 통신 ◑

### ⭕ WebSocket이란?

클라이언트와 서버 간 양방향 실시간 통신을 위한 프로토콜이다.

| 구분 | HTTP | WebSocket |
|------|------|-----------|
| 연결 | 요청-응답 후 종료 | 지속적 연결 유지 |
| 방향 | 단방향 (클라이언트 → 서버) | 양방향 |
| 오버헤드 | 매 요청마다 헤더 | 초기 핸드셰이크 후 경량 |
| 적합 용도 | 일반 웹 페이지 | 실시간 채팅, 게임, 주식 |

### WebSocket 핸드셰이크 ◑

```
클라이언트: HTTP Upgrade 요청 (ws:// 또는 wss://)
서버: 101 Switching Protocols 응답
→ TCP 연결 유지, 양방향 통신 시작
```

### 대안 기술 비교 ◑

| 기술 | 설명 |
|------|------|
| **Polling** | 주기적으로 서버에 요청 |
| **Long Polling** | 응답 올 때까지 연결 유지 |
| **SSE** | 서버 → 클라이언트 단방향 스트림 |
| **WebSocket** | 양방향, 실시간 |

---

## 5. GraphQL ◑

### ⭕ REST와 GraphQL의 차이는?

| 구분 | REST | GraphQL |
|------|------|---------|
| 엔드포인트 | 리소스별 다수 | 단일 엔드포인트 |
| 데이터 요청 | 고정된 응답 구조 | 클라이언트가 원하는 필드만 |
| Over/Under-fetching | 발생 가능 | 해결 |
| 버저닝 | URL 버저닝 | 불필요 (스키마 진화) |
| 캐싱 | HTTP 캐싱 용이 | 별도 구현 필요 |

### GraphQL 기본 개념 ◑

| 개념 | 설명 |
|------|------|
| **Query** | 데이터 조회 |
| **Mutation** | 데이터 변경 |
| **Subscription** | 실시간 업데이트 구독 |
| **Schema** | 타입 정의 (SDL) |
| **Resolver** | 데이터 가져오는 함수 |

### GraphQL 예시 ◑

```graphql
# Query
query {
  user(id: "1") {
    name
    email
    posts {
      title
    }
  }
}

# 응답: 요청한 필드만 반환
{
  "data": {
    "user": {
      "name": "Kim",
      "email": "kim@example.com",
      "posts": [{"title": "Hello"}]
    }
  }
}
```

---

## 6. 웹 렌더링 방식 ◑

### ⭕ CSR과 SSR의 차이는?

| 구분 | CSR (Client-Side Rendering) | SSR (Server-Side Rendering) |
|------|----------------------------|----------------------------|
| 렌더링 위치 | 브라우저 | 서버 |
| 초기 로딩 | 느림 (JS 다운로드 후 렌더링) | 빠름 (완성된 HTML 전송) |
| 이후 탐색 | 빠름 | 페이지마다 서버 요청 |
| SEO | 불리 (빈 HTML) | 유리 (완성된 HTML) |
| 서버 부하 | 낮음 | 높음 |
| 인터랙션 | 부드러움 | Hydration 필요 |

### SPA (Single Page Application) ◑

하나의 HTML 페이지에서 JavaScript로 콘텐츠를 동적으로 변경하는 방식이다.

| 장점 | 단점 |
|------|------|
| 부드러운 사용자 경험 | 초기 로딩 느림 |
| 서버 요청 감소 | SEO 불리 |
| 컴포넌트 재사용 | 브라우저 히스토리 관리 복잡 |

### 렌더링 방식 비교 ◑

| 방식 | 설명 | 적합한 경우 |
|------|------|-------------|
| CSR | 브라우저에서 JS로 렌더링 | 대화형 웹 앱 |
| SSR | 서버에서 HTML 생성 | SEO 중요, 콘텐츠 중심 |
| SSG | 빌드 시 HTML 생성 | 변경 적은 콘텐츠 (블로그) |
| ISR | SSG + 주기적 재생성 | 자주 변경되는 정적 사이트 |

### Hydration ◑

SSR로 전송된 정적 HTML에 JavaScript를 연결하여 인터랙티브하게 만드는 과정이다.

---

## 7. 브라우저 동작 ◑

### ⭕ 브라우저에 URL을 입력하면 무슨 일이 일어나는가?

1. **DNS 조회**: 도메인 → IP 주소 변환
2. **TCP 연결**: 3-way handshake
3. **TLS 핸드셰이크**: HTTPS인 경우
4. **HTTP 요청**: 서버에 리소스 요청
5. **서버 응답**: HTML 반환
6. **파싱 및 렌더링**: DOM 생성, CSSOM 생성, 렌더 트리 구성, 레이아웃, 페인트

### 브라우저 렌더링 과정 ◑

```
HTML 파싱 → DOM 트리
                      ↘
                        렌더 트리 → 레이아웃 → 페인트 → 합성
                      ↗
CSS 파싱 → CSSOM 트리
```

### CORS (Cross-Origin Resource Sharing) ◑

다른 출처(Origin)의 리소스 요청을 허용하는 메커니즘이다.

| 요청 유형 | 설명 |
|----------|------|
| Simple Request | GET, POST, HEAD + 특정 헤더만 사용 |
| Preflight Request | OPTIONS 메서드로 사전 검증 |

---

## 8. 프론트엔드 개념 ◑

### DOM (Document Object Model) ◑

HTML 문서를 트리 구조로 표현한 객체 모델이다.

### Virtual DOM ◑

| 특징 | 설명 |
|------|------|
| 개념 | 실제 DOM의 메모리상 복사본 |
| 목적 | 효율적인 DOM 업데이트 |
| 동작 | 변경 감지 → Diffing → 실제 DOM 최소 업데이트 |

### 이벤트 전파 ◑

| 단계 | 설명 |
|------|------|
| Capturing | 루트 → 타겟 (하향) |
| Target | 이벤트 발생 요소 |
| Bubbling | 타겟 → 루트 (상향) |

**이벤트 위임**: 부모 요소에 이벤트 핸들러를 등록하여 자식 요소의 이벤트를 처리

---

## 9. 백엔드 개념 ◑

### 웹 서버 vs WAS ◑

| 구분 | 웹 서버 | WAS (Web Application Server) |
|------|---------|------------------------------|
| 역할 | 정적 콘텐츠 제공 | 동적 콘텐츠 처리 |
| 예시 | Nginx, Apache | Tomcat, Node.js |

### 미들웨어 ◑

요청과 응답 사이에서 처리되는 함수로, 로깅, 인증, 에러 처리 등에 사용된다.

### 캐싱 ◑

| 위치 | 설명 |
|------|------|
| 브라우저 캐시 | 클라이언트에 저장 |
| CDN | 엣지 서버에 캐시 |
| 서버 캐시 | Redis, Memcached |

---

## 10. 웹 보안

### OWASP Top 10 (주요)

| 취약점 | 설명 | 대응 |
|--------|------|------|
| SQL Injection | 악의적 SQL 삽입 | Prepared Statement 사용 |
| XSS | 악성 스크립트 삽입 | 입력값 이스케이프 |
| CSRF | 사용자 권한 도용 | CSRF 토큰 |

### 보안 헤더 ◑

| 헤더 | 설명 |
|------|------|
| Content-Security-Policy | 허용된 리소스 출처 지정 |
| X-Frame-Options | 클릭재킹 방지 |
| X-Content-Type-Options | MIME 스니핑 방지 |
| Strict-Transport-Security | HTTPS 강제 |

---

## 11. 웹 성능 최적화 ◑

### 프론트엔드 최적화 ◑

| 기법 | 설명 |
|------|------|
| 코드 분할 (Code Splitting) | 필요한 코드만 로드 |
| 이미지 최적화 | WebP, Lazy Loading |
| 번들 최소화 | Minification, Tree Shaking |
| CDN 활용 | 정적 자산 엣지 배포 |

### 핵심 성능 지표 ◑

| 지표 | 설명 |
|------|------|
| LCP (Largest Contentful Paint) | 가장 큰 콘텐츠 렌더링 시간 |
| FID (First Input Delay) | 첫 상호작용 지연 시간 |
| CLS (Cumulative Layout Shift) | 레이아웃 이동 누적 |

---

## 면접 대비 체크리스트 ◑

- [ ] 쿠키, 세션, 토큰 (JWT)의 차이
- [ ] HTTP 메서드와 상태 코드
- [ ] GET vs POST 차이
- [ ] CSR vs SSR 차이와 적합한 사용 사례
- [ ] REST API 설계 원칙
- [ ] 브라우저에 URL 입력 시 동작 과정
- [ ] CORS의 개념과 동작 방식
- [ ] XSS, CSRF 공격과 방어
- [ ] WebSocket과 HTTP의 차이
- [ ] REST vs GraphQL 비교

