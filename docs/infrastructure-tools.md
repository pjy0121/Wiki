# 14. 인프라 도구 (Infrastructure Tools) ◑

현대 백엔드 시스템에서 애플리케이션 서버와 데이터베이스만으로는 부족하다. 캐시, 메시지 큐, 검색 엔진, 모니터링 등 다양한 인프라 도구들이 시스템의 성능, 확장성, 안정성을 담당한다. 이 장에서는 실무에서 가장 많이 사용되는 인프라 도구들을 다룬다.

---

## 1. Redis ◑

### Redis란? ◑

Redis(Remote Dictionary Server)는 **인메모리 키-값 데이터 저장소**이다. 2009년 Salvatore Sanfilippo가 개발했으며, 현재 가장 인기 있는 인메모리 데이터베이스이다.

```
전통적인 아키텍처:
┌────────────┐      ┌────────────┐
│   Client   │ ────→│  Database  │
└────────────┘      └────────────┘
                    (디스크 I/O, 느림)

Redis를 사용한 아키텍처:
┌────────────┐      ┌────────────┐      ┌────────────┐
│   Client   │ ────→│   Redis    │ ←──→ │  Database  │
└────────────┘      └────────────┘      └────────────┘
                    (메모리, 빠름)
```

### ⭕ Redis를 사용하는 이유는?

**1. 초고속 응답**

```
디스크 기반 DB:  수 밀리초 (ms)
Redis (메모리):  마이크로초 (μs)

차이: 100~1000배

디스크 접근:    ~10ms
메모리 접근:    ~100ns (10만 배 빠름)
네트워크 왕복:  ~0.5ms

→ Redis는 네트워크 지연이 병목이 될 정도로 빠름
```

**2. 다양한 자료구조 지원**

Redis는 단순한 키-값 저장소가 아니라 **다양한 자료구조**를 네이티브로 지원한다. 이 점이 Memcached와 가장 큰 차이이다.

**3. 영속성 옵션**

"인메모리"라고 해서 데이터가 사라지는 것이 아니다. 디스크에 저장하여 재시작해도 데이터를 복구할 수 있다.

**4. 복제와 클러스터링**

고가용성과 수평 확장을 위한 기능이 내장되어 있다.

### Redis 자료구조 상세 ◑

**1. String (문자열)**

가장 기본적인 자료구조. 문자열, 숫자, 바이너리 데이터 모두 저장 가능하다.

```redis
SET user:1:name "John"           # 문자열 저장
GET user:1:name                  # "John"

SET counter 100                  # 숫자 저장
INCR counter                     # 101 (원자적 증가)
INCRBY counter 10                # 111

SETEX session:abc123 3600 "user_data"  # 1시간 후 자동 만료

용도:
- 캐시 (HTML, JSON 응답)
- 세션 저장
- 카운터 (조회수, 좋아요)
- Rate Limiting
```

**2. Hash (해시)**

필드-값 쌍의 집합. 객체를 저장하기에 적합하다.

```redis
HSET user:1 name "John" age 30 city "Seoul"
HGET user:1 name                 # "John"
HGETALL user:1                   # name "John" age "30" city "Seoul"
HINCRBY user:1 age 1             # age를 31로 증가

용도:
- 사용자 프로필
- 상품 정보
- 설정 값 저장

장점:
- 전체 객체를 하나의 키로 관리
- 개별 필드만 업데이트 가능 (대역폭 절약)
```

**3. List (리스트)**

순서가 있는 문자열 목록. 양끝에서 삽입/삭제가 O(1)이다.

```redis
LPUSH recent:user:1 "item3" "item2" "item1"  # 왼쪽에 삽입
RPUSH queue:tasks "task1"                     # 오른쪽에 삽입
LPOP queue:tasks                              # 왼쪽에서 제거 (FIFO 큐)
LRANGE recent:user:1 0 9                      # 처음 10개 조회

용도:
- 최근 본 상품 (LPUSH + LTRIM)
- 메시지 큐 (LPUSH + BRPOP)
- 타임라인

LTRIM recent:user:1 0 99  # 최근 100개만 유지
```

**4. Set (집합)**

중복 없는 문자열 집합. 집합 연산(합집합, 교집합, 차집합)이 가능하다.

```redis
SADD user:1:tags "tech" "programming" "redis"
SADD user:2:tags "tech" "database" "redis"

SMEMBERS user:1:tags             # 모든 멤버 조회
SISMEMBER user:1:tags "tech"     # 멤버 여부 확인 (O(1))

SINTER user:1:tags user:2:tags   # 교집합: "tech", "redis"
SUNION user:1:tags user:2:tags   # 합집합
SDIFF user:1:tags user:2:tags    # 차집합: "programming"

용도:
- 태그 시스템
- 친구 관계 (공통 친구 = 교집합)
- 좋아요한 사용자 목록
- 고유 방문자 집계
```

**5. Sorted Set (정렬된 집합)**

각 멤버에 점수(score)가 부여되어 **자동 정렬**되는 집합이다.

```redis
ZADD leaderboard 100 "player1" 85 "player2" 92 "player3"

ZRANGE leaderboard 0 -1 WITHSCORES    # 점수 오름차순
ZREVRANGE leaderboard 0 2             # 점수 내림차순 상위 3명
ZRANK leaderboard "player2"           # 순위 조회 (0부터 시작)
ZINCRBY leaderboard 5 "player2"       # 점수 증가

용도:
- 리더보드/랭킹
- 우선순위 큐
- 시간순 정렬 (타임스탬프를 score로)
- Rate Limiting (슬라이딩 윈도우)

특징:
- 삽입: O(log N)
- 조회: O(log N + M)
- Skip List 자료구조 사용
```

**6. Stream (스트림)**

Redis 5.0에서 추가된 **로그 형태의 자료구조**. Kafka와 유사한 기능을 제공한다.

```redis
XADD stream:orders * product "laptop" price 1000
# 1693580400000-0 (자동 생성된 ID)

XREAD STREAMS stream:orders 0           # 처음부터 읽기
XREAD BLOCK 5000 STREAMS stream:orders $  # 새 메시지 대기 (5초)

# Consumer Group
XGROUP CREATE stream:orders group1 0
XREADGROUP GROUP group1 consumer1 COUNT 10 STREAMS stream:orders >

용도:
- 이벤트 스트리밍
- 메시지 큐 (컨슈머 그룹)
- 실시간 알림
```

### ⭕ Redis 영속성 (Persistence) ◑

Redis는 인메모리 DB이지만 **데이터를 디스크에 저장**할 수 있다.

**1. RDB (Redis Database Backup)**

```
특정 시점의 스냅샷을 저장:

메모리 상태:
┌─────────────────────────┐
│ key1=value1             │
│ key2=value2             │
│ ...                     │
└─────────────────────────┘
         │
         ▼ (BGSAVE)
┌─────────────────────────┐
│ dump.rdb (압축된 바이너리) │
└─────────────────────────┘

설정 예시 (redis.conf):
save 900 1      # 15분 내 1개 이상 변경 시 저장
save 300 10     # 5분 내 10개 이상 변경 시 저장
save 60 10000   # 1분 내 10000개 이상 변경 시 저장
```

| 장점 | 단점 |
|------|------|
| 복구가 빠름 (파일 로드) | 마지막 스냅샷 이후 데이터 손실 가능 |
| 파일 크기가 작음 (압축) | 스냅샷 생성 시 CPU 사용 |
| 백업/복원이 단순 | fork() 시 메모리 사용 증가 |

**2. AOF (Append-Only File)**

```
모든 쓰기 명령을 로그로 기록:

명령 실행:
SET key1 value1
SET key2 value2
INCR counter

appendonly.aof:
*3\r\n$3\r\nSET\r\n$4\r\nkey1\r\n$6\r\nvalue1\r\n
*3\r\n$3\r\nSET\r\n$4\r\nkey2\r\n$6\r\nvalue2\r\n
*2\r\n$4\r\nINCR\r\n$7\r\ncounter\r\n

설정:
appendonly yes
appendfsync everysec    # 1초마다 디스크에 동기화 (권장)
                        # always: 매 명령마다 (느림, 안전)
                        # no: OS에 맡김 (빠름, 위험)
```

| 장점 | 단점 |
|------|------|
| 데이터 손실 최소화 (최대 1초) | 파일 크기가 큼 |
| 사람이 읽을 수 있는 로그 | 복구가 느림 (명령 재실행) |
| 손상된 파일 복구 도구 제공 | AOF rewrite 필요 |

**3. RDB + AOF (권장)**

```
두 가지를 모두 사용:
- RDB: 정기적 백업, 빠른 복구
- AOF: 실시간 내구성

복구 시 AOF 파일이 있으면 AOF 사용 (더 완전한 데이터)
```

### ⭕ Redis 클러스터 ◑

**1. Master-Replica (복제)**

```
쓰기 요청:
┌──────────┐
│  Master  │ ← 모든 쓰기
└────┬─────┘
     │ 복제 (비동기)
┌────┴─────┐
▼          ▼
┌────────┐ ┌────────┐
│Replica1│ │Replica2│ ← 읽기 분산
└────────┘ └────────┘

장점:
- 읽기 성능 향상 (읽기 분산)
- 장애 복구 (Replica를 Master로 승격)
- 백업 (Replica에서 RDB 생성)
```

**2. Sentinel (자동 페일오버)**

```
┌──────────────────────────────────────────────┐
│              Sentinel Cluster                │
│    ┌──────────┐ ┌──────────┐ ┌──────────┐   │
│    │Sentinel 1│ │Sentinel 2│ │Sentinel 3│   │
│    └────┬─────┘ └────┬─────┘ └────┬─────┘   │
│         │ 모니터링    │            │          │
└─────────┼────────────┼────────────┼──────────┘
          ▼            ▼            ▼
     ┌──────────┐  ┌──────────┐  ┌──────────┐
     │  Master  │  │ Replica1 │  │ Replica2 │
     └──────────┘  └──────────┘  └──────────┘

동작:
1. Sentinel들이 Master 상태 모니터링
2. Master 장애 감지 (quorum 합의)
3. Replica 중 하나를 새 Master로 선출
4. 클라이언트에게 새 Master 정보 전달
```

**3. Cluster (데이터 샤딩)**

```
16384개의 해시 슬롯을 노드에 분배:

┌─────────────────────────────────────────────────────┐
│                  Redis Cluster                      │
│                                                     │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐ │
│  │   Node 1    │  │   Node 2    │  │   Node 3    │ │
│  │ Slot 0-5460 │  │Slot 5461-10922│ │Slot 10923-16383│ │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘ │
│         │                │                │         │
│    ┌────┴────┐      ┌────┴────┐      ┌────┴────┐   │
│    │ Replica │      │ Replica │      │ Replica │   │
│    └─────────┘      └─────────┘      └─────────┘   │
│                                                     │
└─────────────────────────────────────────────────────┘

키 배치:
slot = CRC16(key) % 16384

예: "user:1000" → CRC16("user:1000") % 16384 = 7532 → Node 2

특징:
- 자동 샤딩
- 노드 추가/제거 시 자동 리밸런싱
- 최소 3개 마스터 노드 권장
```

### Redis 실무 사용 사례 ◑

```
1. 캐시 (가장 흔한 용도):
   GET /products/123
   → Redis에서 조회 → 있으면 반환
   → 없으면 DB 조회 → Redis에 저장 → 반환

2. 세션 저장:
   SET session:abc123 "{user_id: 1, ...}" EX 3600

3. Rate Limiting:
   INCR rate:user:1:minute
   EXPIRE rate:user:1:minute 60
   → 카운트가 100 초과하면 요청 거부

4. 분산 락:
   SET lock:resource1 "owner1" NX EX 10
   → NX: 키가 없을 때만 설정 (원자적)
   → 락 획득 성공/실패 구분

5. 리더보드:
   ZINCRBY game:scores 100 "player1"
   ZREVRANGE game:scores 0 9 WITHSCORES
```

---

## 2. Apache Kafka ◑

### Kafka란? ◑

Kafka는 LinkedIn에서 개발한 **분산 이벤트 스트리밍 플랫폼**이다. 2011년 오픈소스로 공개되었으며, 현재 대규모 데이터 파이프라인의 표준이 되었다.

```
전통적인 아키텍처 (Point-to-Point):
┌────────┐     ┌────────┐
│Service1│────→│Service2│
└────────┘     └────────┘
    │              │
    ▼              ▼
┌────────┐     ┌────────┐
│Service3│────→│Service4│
└────────┘     └────────┘
→ 서비스가 늘어날수록 연결이 기하급수적으로 증가

Kafka를 사용한 아키텍처 (Pub-Sub):
┌────────┐   ┌────────┐   ┌────────┐
│Service1│   │Service2│   │Service3│
└───┬────┘   └───┬────┘   └───┬────┘
    │            │            │
    ▼            ▼            ▼
┌─────────────────────────────────────┐
│              Kafka                  │
└─────────────────────────────────────┘
    │            │            │
    ▼            ▼            ▼
┌────────┐   ┌────────┐   ┌────────┐
│Service4│   │Service5│   │Service6│
└────────┘   └────────┘   └────────┘
→ 모든 서비스가 Kafka를 통해 통신, 느슨한 결합
```

### ⭕ Kafka를 사용하는 이유는?

| 특징 | 설명 |
|------|------|
| **높은 처리량** | 초당 수백만 메시지 처리 가능 |
| **내구성** | 디스크에 저장, 복제로 데이터 보호 |
| **확장성** | 파티션 추가로 수평 확장 |
| **재처리 가능** | 오프셋 조절로 과거 메시지 재소비 |
| **실시간 + 배치** | 스트림 처리와 배치 처리 모두 지원 |

### Kafka 핵심 개념 ◑

```
Kafka 아키텍처:

Producer ──────────┐
Producer ──────────┤
Producer ──────────┤
                   ▼
┌─────────────────────────────────────────────────────────┐
│                    Kafka Cluster                        │
│                                                         │
│   Topic: "user-events"                                  │
│   ┌─────────────┬─────────────┬─────────────┐          │
│   │ Partition 0 │ Partition 1 │ Partition 2 │          │
│   │ [0,1,2,3,4] │ [0,1,2,3]   │ [0,1,2,3,4,5]│         │
│   │ Leader: B1  │ Leader: B2  │ Leader: B3  │          │
│   │ Replica: B2 │ Replica: B3 │ Replica: B1 │          │
│   └─────────────┴─────────────┴─────────────┘          │
│                                                         │
│   Broker 1        Broker 2        Broker 3             │
│                                                         │
└─────────────────────────────────────────────────────────┘
                   │
                   ▼
           ┌──────────────┐
           │Consumer Group│
           │ Consumer 1   │ ← Partition 0
           │ Consumer 2   │ ← Partition 1, 2
           └──────────────┘
```

**핵심 용어 설명**

| 개념 | 설명 |
|------|------|
| **Producer** | 메시지를 Kafka에 발행하는 클라이언트 |
| **Consumer** | 메시지를 Kafka에서 소비하는 클라이언트 |
| **Topic** | 메시지의 논리적 카테고리 (예: "user-events", "orders") |
| **Partition** | 토픽을 물리적으로 분할한 단위. **순서 보장의 단위** |
| **Broker** | Kafka 서버 노드 |
| **Offset** | 파티션 내 메시지의 고유 순번 (0, 1, 2, ...) |
| **Consumer Group** | 파티션을 분담하여 병렬 처리하는 컨슈머 집합 |

**Partition과 순서 보장**

```
Partition은 Kafka 설계의 핵심이다:

1. 병렬 처리:
   - 파티션 수 = 최대 병렬 처리 수
   - 3개 파티션 → 최대 3개 컨슈머가 병렬 처리

2. 순서 보장:
   - 파티션 내에서만 순서 보장
   - 파티션 간 순서는 보장 안 됨

   예: 같은 사용자의 이벤트는 같은 파티션으로
   key="user123" → hash("user123") % 3 = Partition 1
   → 해당 사용자의 모든 이벤트는 순서대로 처리

3. 확장성:
   - 파티션 추가 = 처리량 증가
   - 단, 한번 늘리면 줄일 수 없음
```

**Consumer Group의 동작**

```
Consumer Group "analytics":
┌─────────────────────────────────────────┐
│                                         │
│  Partition 0 ────→ Consumer A          │
│  Partition 1 ────→ Consumer B          │
│  Partition 2 ────→ Consumer B          │
│                                         │
└─────────────────────────────────────────┘

규칙:
- 한 파티션은 그룹 내 하나의 컨슈머만 소비
- 컨슈머 수 > 파티션 수 → 일부 컨슈머는 놀게 됨
- 컨슈머 장애 시 → 해당 파티션이 다른 컨슈머에게 재할당

다른 Consumer Group "logging":
┌─────────────────────────────────────────┐
│                                         │
│  Partition 0 ────→ Consumer X          │
│  Partition 1 ────→ Consumer Y          │
│  Partition 2 ────→ Consumer Z          │
│                                         │
└─────────────────────────────────────────┘

→ 각 그룹은 독립적으로 모든 메시지를 소비
→ 같은 데이터를 다른 목적으로 처리 가능
```

### ⭕ Kafka vs RabbitMQ ◑

| 구분 | Kafka | RabbitMQ |
|------|-------|----------|
| **모델** | 로그 기반 (Pull) | 큐 기반 (Push) |
| **메시지 보존** | 설정 기간 동안 보존 | 소비 후 삭제 |
| **순서 보장** | 파티션 내 보장 | 큐 내 보장 |
| **처리량** | 초당 수백만 | 초당 수만 |
| **재처리** | Offset 리셋으로 쉬움 | 어려움 (DLQ 사용) |
| **라우팅** | 단순 (파티션) | 복잡 (Exchange 타입) |
| **적합 용도** | 로그, 이벤트 스트리밍, 대용량 | 작업 큐, 트랜잭션 메시징, 복잡한 라우팅 |

```
언제 Kafka?
- 이벤트 소싱, 로그 수집
- 대용량 데이터 파이프라인
- 데이터를 여러 시스템에서 소비
- 메시지 재처리가 필요한 경우

언제 RabbitMQ?
- 전통적인 작업 큐
- 복잡한 라우팅 규칙
- 요청-응답 패턴
- 트랜잭션이 중요한 경우
```

### Kafka 메시지 전달 보장 ◑

```
Producer 설정: acks

acks=0:
Producer → Broker (응답 대기 안 함)
→ 가장 빠름, 메시지 손실 가능
→ At-most-once

acks=1:
Producer → Broker (리더만 확인)
         ← ACK
→ 리더 장애 시 손실 가능

acks=all:
Producer → Broker (모든 ISR 복제 완료 확인)
         ← ACK
→ 가장 안전, 느림
→ At-least-once (중복 가능)

acks=all + enable.idempotence=true:
→ Exactly-once (중복 제거)
→ Producer가 동일 메시지 재전송 시 자동 감지
```

### Kafka Streams와 ksqlDB ◑

**Kafka Streams**: Java 라이브러리로 스트림 처리 애플리케이션 개발

```java
StreamsBuilder builder = new StreamsBuilder();

// 실시간 단어 카운트
KStream<String, String> textLines = builder.stream("text-input");
KTable<String, Long> wordCounts = textLines
    .flatMapValues(value -> Arrays.asList(value.split(" ")))
    .groupBy((key, word) -> word)
    .count();

wordCounts.toStream().to("word-count-output");
```

**ksqlDB**: SQL로 스트림 처리

```sql
CREATE STREAM orders (
  order_id VARCHAR,
  user_id VARCHAR,
  amount DECIMAL
) WITH (
  KAFKA_TOPIC='orders',
  VALUE_FORMAT='JSON'
);

-- 실시간 집계
SELECT user_id, SUM(amount) as total
FROM orders
WINDOW TUMBLING (SIZE 1 HOUR)
GROUP BY user_id
EMIT CHANGES;
```

---

## 3. Elasticsearch ◑

### Elasticsearch란? ◑

Elasticsearch는 Apache Lucene 기반의 **분산 검색 및 분석 엔진**이다. 전문 검색(Full-text search)과 실시간 분석에 특화되어 있다.

```
RDBMS로 검색:
SELECT * FROM products WHERE description LIKE '%laptop%'
→ 전체 테이블 스캔, 느림
→ "랩톱", "노트북" 동의어 처리 불가

Elasticsearch로 검색:
GET /products/_search
{ "query": { "match": { "description": "laptop" }}}
→ 역인덱스로 빠른 검색
→ 형태소 분석, 동의어, 유사어 처리
```

### ⭕ Elasticsearch를 사용하는 이유는?

| 특징 | 설명 |
|------|------|
| **전문 검색** | 역인덱스 기반 빠른 텍스트 검색 |
| **실시간** | Near real-time (문서 인덱싱 후 1초 내 검색 가능) |
| **분산** | 샤딩으로 수평 확장, 복제로 고가용성 |
| **분석** | Aggregation으로 실시간 데이터 분석 |
| **스키마리스** | JSON 문서, 동적 매핑 |

### 역인덱스 (Inverted Index) ◑

Elasticsearch의 핵심은 **역인덱스**이다.

```
일반 인덱스 (Forward Index):
문서 ID → 내용

Doc1: "Redis is fast"
Doc2: "Kafka is distributed"
Doc3: "Redis and Kafka"

역인덱스 (Inverted Index):
단어 → 문서 ID 목록

"redis"       → [Doc1, Doc3]
"kafka"       → [Doc2, Doc3]
"fast"        → [Doc1]
"distributed" → [Doc2]
"and"         → [Doc3]

검색 "redis kafka":
1. "redis" → [Doc1, Doc3]
2. "kafka" → [Doc2, Doc3]
3. 교집합: [Doc3]
4. 또는 합집합: [Doc1, Doc2, Doc3]

→ 전체 문서 스캔 없이 O(1)에 가까운 검색
```

**텍스트 분석 (Text Analysis)**

```
원본 텍스트: "The Quick Brown Fox Jumps Over The Lazy Dog"

분석 과정:
1. 토큰화 (Tokenization):
   ["The", "Quick", "Brown", "Fox", "Jumps", "Over", "The", "Lazy", "Dog"]

2. 소문자 변환 (Lowercase):
   ["the", "quick", "brown", "fox", "jumps", "over", "the", "lazy", "dog"]

3. 불용어 제거 (Stop words):
   ["quick", "brown", "fox", "jumps", "lazy", "dog"]

4. 형태소 분석 (Stemming):
   ["quick", "brown", "fox", "jump", "lazi", "dog"]

→ "jumping", "jumped" 검색해도 "jumps" 문서 찾음
```

### Elasticsearch 핵심 개념 ◑

```
Elasticsearch vs RDBMS 비교:

RDBMS                    Elasticsearch
─────────                ─────────────
Database        ≈        Index
Table           ≈        Type (7.x부터 deprecated)
Row             ≈        Document
Column          ≈        Field
Schema          ≈        Mapping
```

**인덱스, 샤드, 복제본**

```
Index: "products" (5 Primary Shards, 1 Replica)

┌─────────────────────────────────────────────────────────────┐
│                     Elasticsearch Cluster                   │
│                                                             │
│  Node 1            Node 2            Node 3                │
│  ┌─────────────┐   ┌─────────────┐   ┌─────────────┐      │
│  │ P0 (Primary)│   │ P1 (Primary)│   │ P2 (Primary)│      │
│  │ R1 (Replica)│   │ R2 (Replica)│   │ R0 (Replica)│      │
│  │ P3 (Primary)│   │ R4 (Replica)│   │ P4 (Primary)│      │
│  │ R3 (Replica)│   │             │   │             │      │
│  └─────────────┘   └─────────────┘   └─────────────┘      │
│                                                             │
└─────────────────────────────────────────────────────────────┘

특징:
- Primary Shard: 원본 데이터
- Replica Shard: Primary의 복사본
- 같은 샤드의 Primary와 Replica는 다른 노드에 배치
- 노드 장애 시 Replica가 Primary로 승격
```

**기본 CRUD 연산**

```json
// 문서 인덱싱 (생성/수정)
PUT /products/_doc/1
{
  "name": "Laptop",
  "price": 1000,
  "description": "Powerful laptop for developers"
}

// 문서 조회
GET /products/_doc/1

// 문서 검색
GET /products/_search
{
  "query": {
    "match": {
      "description": "laptop developers"
    }
  }
}

// 문서 삭제
DELETE /products/_doc/1
```

### ELK Stack ◑

```
┌─────────────────────────────────────────────────────────────┐
│                       ELK Stack                             │
│                                                             │
│  ┌──────────┐    ┌──────────┐    ┌──────────────────────┐  │
│  │  Beats   │───→│ Logstash │───→│    Elasticsearch     │  │
│  │(경량 수집)│    │ (변환)   │    │    (저장/검색)       │  │
│  └──────────┘    └──────────┘    └──────────────────────┘  │
│                                           │                 │
│                                           ▼                 │
│                                    ┌──────────┐            │
│                                    │  Kibana  │            │
│                                    │ (시각화) │            │
│                                    └──────────┘            │
│                                                             │
└─────────────────────────────────────────────────────────────┘

역할:
- Beats: 서버에서 로그/메트릭 수집 (Filebeat, Metricbeat)
- Logstash: 데이터 변환, 필터링, 라우팅
- Elasticsearch: 저장, 인덱싱, 검색
- Kibana: 시각화 대시보드, 쿼리 UI
```

**로그 분석 파이프라인 예시**

```
웹 서버 로그:
192.168.1.1 - - [10/Oct/2023:13:55:36] "GET /api/users HTTP/1.1" 200 1234

Logstash 필터:
grok {
  match => { "message" => "%{IP:client} .* \[%{HTTPDATE:timestamp}\] \"%{WORD:method} %{URIPATH:path}.*\" %{NUMBER:status} %{NUMBER:bytes}" }
}

Elasticsearch 문서:
{
  "client": "192.168.1.1",
  "timestamp": "2023-10-10T13:55:36",
  "method": "GET",
  "path": "/api/users",
  "status": 200,
  "bytes": 1234
}

Kibana:
- 시간별 요청 수 그래프
- 상태 코드별 분포
- 느린 요청 top 10
```

---

## 4. RabbitMQ ◑

### RabbitMQ란? ◑

RabbitMQ는 **AMQP(Advanced Message Queuing Protocol)** 를 구현한 오픈소스 메시지 브로커이다. 복잡한 라우팅과 신뢰성 있는 메시지 전달에 강점이 있다.

### RabbitMQ 아키텍처 ◑

```
┌────────────────────────────────────────────────────────────────┐
│                        RabbitMQ                                │
│                                                                │
│  Producer ──→ Exchange ──→ Binding ──→ Queue ──→ Consumer     │
│                   │                      │                     │
│                   │   Routing Key       │                     │
│                   └──────────────────────┘                     │
│                                                                │
└────────────────────────────────────────────────────────────────┘

핵심 컴포넌트:
- Exchange: 메시지를 받아서 라우팅 규칙에 따라 큐로 전달
- Queue: 메시지를 저장하는 버퍼
- Binding: Exchange와 Queue를 연결하는 규칙
- Routing Key: 메시지 라우팅 기준
```

### Exchange 타입 ◑

**1. Direct Exchange**

```
정확한 라우팅 키 매칭:

Producer ──→ Exchange (Direct)
                │
                ├── routing_key="error" ──→ Queue: error-logs
                └── routing_key="info"  ──→ Queue: info-logs

사용 예: 로그 레벨별 분리
```

**2. Fanout Exchange**

```
모든 바인딩된 큐에 브로드캐스트:

Producer ──→ Exchange (Fanout)
                │
                ├──────────────────────→ Queue: analytics
                ├──────────────────────→ Queue: logging
                └──────────────────────→ Queue: notification

라우팅 키 무시

사용 예: 이벤트를 여러 시스템에 전파
```

**3. Topic Exchange**

```
패턴 매칭:
* = 정확히 하나의 단어
# = 0개 이상의 단어

Producer ──→ Exchange (Topic)
                │
                ├── pattern="*.error"    ──→ Queue: all-errors
                ├── pattern="order.*"    ──→ Queue: order-events
                └── pattern="order.#"    ──→ Queue: all-order-events

예시:
routing_key="order.error"  → all-errors, all-order-events 둘 다 매칭
routing_key="order.created" → order-events, all-order-events

사용 예: 이벤트 타입별 필터링
```

**4. Headers Exchange**

```
메시지 헤더 속성으로 라우팅:

Producer ──→ Exchange (Headers)
                │
                └── headers: {format: "pdf", type: "report"}
                    ├── x-match: all → 모든 헤더 매칭
                    └── x-match: any → 하나라도 매칭

사용 예: 복잡한 라우팅 조건
```

### 메시지 신뢰성 보장 ◑

```
Producer Confirms:
Producer ──→ Broker
         ←── ACK (저장 확인)

메시지가 브로커에 저장되었음을 확인

Consumer Acknowledgments:
         Broker ──→ Consumer
                ←── ACK (처리 완료)

메시지 처리 완료 후 ACK를 보내야 큐에서 삭제

auto-ack=false (권장):
1. 메시지 전달
2. Consumer가 처리
3. 명시적 ACK
4. 큐에서 삭제

→ Consumer 장애 시 메시지가 다른 Consumer에게 재전달
```

**Dead Letter Queue (DLQ)**

```
처리 실패한 메시지를 별도 큐로:

Main Queue ──→ Consumer (처리 실패)
     │
     └── Dead Letter Exchange ──→ DLQ

DLQ 트리거:
- 메시지 거부 (nack/reject)
- TTL 만료
- 큐 용량 초과

사용 예: 실패 메시지 분석, 재처리 파이프라인
```

---

## 5. 관측성 도구 (Observability) ◑

### 관측성의 세 가지 축 ◑

```
┌─────────────────────────────────────────────────────────────┐
│                     Observability                           │
│                                                             │
│    ┌──────────┐    ┌──────────┐    ┌──────────┐           │
│    │  Logs    │    │ Metrics  │    │  Traces  │           │
│    │ (로그)   │    │ (메트릭) │    │ (트레이스)│           │
│    └──────────┘    └──────────┘    └──────────┘           │
│         │              │              │                    │
│    "무엇이"        "얼마나"       "어디서"                 │
│    "발생했나"      "빠른가"       "발생했나"               │
│                                                             │
└─────────────────────────────────────────────────────────────┘

Logs: 이벤트 기록 (에러 메시지, 디버그 정보)
Metrics: 시계열 숫자 데이터 (CPU 사용률, 요청 수)
Traces: 요청의 전체 경로 추적 (MSA에서 중요)
```

### Prometheus + Grafana ◑

```
┌─────────────────────────────────────────────────────────────┐
│                   Prometheus Stack                          │
│                                                             │
│  ┌──────────┐    ┌──────────┐                              │
│  │ App 1    │←──┤          │                              │
│  │ /metrics │    │Prometheus│──→ AlertManager ──→ Slack   │
│  └──────────┘    │ (Pull)   │                              │
│  ┌──────────┐    │          │                              │
│  │ App 2    │←──┤          │                              │
│  │ /metrics │    │          │                              │
│  └──────────┘    └────┬─────┘                              │
│                       │                                     │
│                       ▼                                     │
│                 ┌──────────┐                               │
│                 │ Grafana  │                               │
│                 │(시각화)  │                               │
│                 └──────────┘                               │
│                                                             │
└─────────────────────────────────────────────────────────────┘

Prometheus:
- Pull 모델: 주기적으로 대상에서 메트릭 수집
- 시계열 DB: 시간+메트릭 데이터 저장
- PromQL: 강력한 쿼리 언어
- 알림: AlertManager와 연동

Grafana:
- 다양한 데이터소스 지원
- 대시보드 생성 및 공유
- 알림 설정
```

**PromQL 예시**

```promql
# 5분간 평균 CPU 사용률
avg(rate(cpu_usage_seconds_total[5m])) by (instance)

# HTTP 요청 에러율
sum(rate(http_requests_total{status=~"5.."}[5m]))
  / sum(rate(http_requests_total[5m]))

# 95 퍼센타일 응답 시간
histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))
```

### 분산 트레이싱 ◑

MSA 환경에서 **요청이 여러 서비스를 거치는 전체 경로**를 추적한다.

```
단일 요청의 여정:

User ──→ API Gateway ──→ Auth Service ──→ User Service ──→ Database
              │               │                │
              ▼               ▼                ▼
           [Span 1]       [Span 2]         [Span 3]
              └─────────────┴─────────────────┘
                         Trace

Trace: 전체 요청 단위
Span: 각 서비스에서의 작업 단위

각 Span 정보:
- 서비스 이름
- 작업 이름
- 시작/종료 시간
- 태그, 로그
- 부모 Span ID (인과 관계)
```

**Jaeger/Zipkin UI 예시**

```
Trace ID: abc123

Timeline:
────────────────────────────────────────────────────────────────
api-gateway    [██████████████████████████████████] 500ms
               └─ auth-service [████████] 100ms
               └─ user-service [██████████████████████] 300ms
                               └─ database [████████] 80ms
────────────────────────────────────────────────────────────────

→ 전체 요청 500ms 중 user-service가 300ms 사용
→ 병목 지점 식별 가능
```

**OpenTelemetry**

```
OpenTelemetry: 관측성 데이터의 표준화된 수집 프레임워크

이전:                         OpenTelemetry:
App ──→ Jaeger Agent         App ──→ OTEL Collector ──→ Jaeger
App ──→ Prometheus                                 ──→ Prometheus
App ──→ Elastic Agent                              ──→ Elasticsearch

하나의 SDK로 Logs, Metrics, Traces 모두 수집
백엔드는 자유롭게 선택 가능
```

---

## 6. 도구 선택 가이드 ◑

### 메시지 브로커 선택 ◑

| 상황 | 추천 |
|------|------|
| 대용량 로그/이벤트 스트리밍 | **Kafka** |
| 메시지 재처리가 필요한 경우 | **Kafka** |
| 복잡한 라우팅 규칙 | **RabbitMQ** |
| 요청-응답 패턴 | **RabbitMQ** |
| 간단한 실시간 알림 | **Redis Pub/Sub** |
| AWS 환경, 관리형 서비스 | **Amazon SQS/SNS** |

### 캐시 선택 ◑

| 상황 | 추천 |
|------|------|
| 범용 캐시 | **Redis** |
| 단순 키-값만 필요 | Memcached |
| 복잡한 자료구조 | **Redis** |
| 분산 락 | **Redis (Redlock)** |
| JVM 로컬 캐시 | Caffeine |
| HTTP/CDN 캐시 | Varnish, CloudFront |

### 검색 엔진 선택 ◑

| 상황 | 추천 |
|------|------|
| 전문 검색 | **Elasticsearch**, OpenSearch |
| 로그 분석 | **Elasticsearch + Kibana** |
| 벡터 검색 (AI) | Milvus, Pinecone, Elasticsearch |
| 간단한 검색 | PostgreSQL Full-text |
| 관리형 서비스 | Algolia, AWS OpenSearch |

---

## 면접 대비 체크리스트 ◑

- [ ] Redis 자료구조 5가지와 각각의 용도
- [ ] Redis 영속성: RDB vs AOF 차이와 선택 기준
- [ ] Redis 클러스터: Sentinel vs Cluster 차이
- [ ] Kafka 핵심 개념: Topic, Partition, Consumer Group
- [ ] Kafka에서 순서를 보장하는 방법 (같은 키 → 같은 파티션)
- [ ] Kafka vs RabbitMQ: 언제 어떤 것을 선택하는가?
- [ ] Kafka 메시지 전달 보장 수준 (acks 설정)
- [ ] Elasticsearch 역인덱스의 원리
- [ ] ELK Stack 각 컴포넌트의 역할
- [ ] RabbitMQ Exchange 타입 4가지
- [ ] Prometheus와 Grafana의 역할
- [ ] 분산 트레이싱의 목적과 Trace/Span 개념

