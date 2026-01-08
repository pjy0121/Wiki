# 14. 인프라 도구 (Infrastructure Tools) ◑

---

## 1. Redis ◑

### Redis란?

인메모리 키-값 데이터 저장소로, 캐시, 세션 저장소, 메시지 브로커 등 다양한 용도로 사용된다.

### ⭕ Redis를 사용하는 이유는?

| 특징 | 설명 |
|------|------|
| **초고속** | 인메모리 기반, 마이크로초 단위 응답 |
| **다양한 자료구조** | String, Hash, List, Set, Sorted Set, Stream 등 |
| **영속성 옵션** | RDB 스냅샷, AOF 로그 |
| **복제/클러스터** | 고가용성, 수평 확장 |

### Redis 자료구조 ◑

| 자료구조 | 용도 | 명령어 예시 |
|----------|------|-------------|
| **String** | 캐시, 카운터 | `SET`, `GET`, `INCR` |
| **Hash** | 객체 저장 | `HSET`, `HGET`, `HGETALL` |
| **List** | 큐, 최근 항목 | `LPUSH`, `RPOP`, `LRANGE` |
| **Set** | 고유 값 집합, 태그 | `SADD`, `SMEMBERS`, `SINTER` |
| **Sorted Set** | 랭킹, 리더보드 | `ZADD`, `ZRANGE`, `ZRANK` |
| **Stream** | 이벤트 로그, 메시징 | `XADD`, `XREAD`, `XREADGROUP` |

### ⭕ Redis 영속성 (Persistence) ◑

| 방식 | 설명 | 장점 | 단점 |
|------|------|------|------|
| **RDB** | 특정 시점 스냅샷 | 복구 빠름, 파일 작음 | 데이터 손실 가능 |
| **AOF** | 모든 쓰기 명령 로그 | 데이터 손실 최소화 | 파일 크기 큼, 복구 느림 |
| **RDB + AOF** | 둘 다 사용 | 안정성과 성능 균형 | 디스크 사용량 증가 |

### Redis 캐시 전략 ◑

| 패턴 | 설명 |
|------|------|
| **Cache-Aside** | 애플리케이션이 캐시 관리 |
| **Write-Through** | 캐시와 DB 동시 쓰기 |
| **Write-Behind** | 캐시 먼저, 나중에 DB 반영 |

### ⭕ Redis 클러스터 ◑

| 구성 | 설명 |
|------|------|
| **Master-Replica** | 읽기 분산, 장애 복구 |
| **Sentinel** | 자동 페일오버 관리 |
| **Cluster** | 데이터 샤딩, 수평 확장 (16384 슬롯) |

---

## 2. Apache Kafka ◑

### Kafka란?

대용량 실시간 데이터 스트리밍을 위한 분산 이벤트 플랫폼이다.

### ⭕ Kafka를 사용하는 이유는?

| 특징 | 설명 |
|------|------|
| **높은 처리량** | 초당 수백만 메시지 처리 |
| **내구성** | 디스크 기반 저장, 복제 |
| **확장성** | 파티션 기반 수평 확장 |
| **실시간 처리** | 스트림 처리 지원 |

### Kafka 핵심 개념 ◑

```
Producer → Topic (Partition 0, 1, 2...) → Consumer Group
                    ↓
              Broker Cluster
```

| 개념 | 설명 |
|------|------|
| **Producer** | 메시지 발행자 |
| **Consumer** | 메시지 구독자 |
| **Topic** | 메시지 카테고리 (논리적 채널) |
| **Partition** | 토픽의 물리적 분할, 순서 보장 단위 |
| **Broker** | Kafka 서버 노드 |
| **Consumer Group** | 파티션을 나눠 소비하는 컨슈머 집합 |
| **Offset** | 파티션 내 메시지 위치 |

### ⭕ Kafka vs RabbitMQ ◑

| 구분 | Kafka | RabbitMQ |
|------|-------|----------|
| 모델 | 로그 기반 (Pull) | 큐 기반 (Push) |
| 메시지 보존 | 설정 기간 동안 보존 | 소비 후 삭제 |
| 순서 보장 | 파티션 내 보장 | 큐 내 보장 |
| 처리량 | 매우 높음 | 높음 |
| 재처리 | Offset 리셋으로 가능 | 어려움 (DLQ 사용) |
| 적합 용도 | 로그, 이벤트 스트리밍, 대용량 | 작업 큐, 트랜잭션 메시징 |

### Kafka 메시지 전달 보장 ◑

| 설정 | 동작 | 보장 수준 |
|------|------|----------|
| `acks=0` | 응답 대기 안 함 | At-most-once |
| `acks=1` | 리더만 확인 | - |
| `acks=all` | 모든 ISR 확인 | At-least-once |
| + 멱등성 | 중복 제거 | Exactly-once |

### Kafka Streams vs ksqlDB ◑

| 도구 | 특징 |
|------|------|
| **Kafka Streams** | Java 라이브러리, 애플리케이션에 임베디드 |
| **ksqlDB** | SQL 기반 스트림 처리, 별도 서버 |

---

## 3. Elasticsearch ◑

### Elasticsearch란?

분산 검색 및 분석 엔진으로, 대용량 텍스트 검색과 로그 분석에 사용된다.

### ⭕ Elasticsearch를 사용하는 이유는?

| 특징 | 설명 |
|------|------|
| **전문 검색** | 역인덱스 기반 빠른 텍스트 검색 |
| **실시간 분석** | Near real-time 인덱싱 |
| **분산 아키텍처** | 샤딩, 복제로 확장성/가용성 |
| **RESTful API** | JSON 기반 간편한 인터페이스 |

### Elasticsearch 핵심 개념 ◑

| 개념 | RDBMS 대응 | 설명 |
|------|-----------|------|
| **Index** | Database | 문서 모음 |
| **Document** | Row | JSON 형태의 데이터 단위 |
| **Field** | Column | 문서 내 속성 |
| **Shard** | - | 인덱스의 물리적 분할 |
| **Replica** | - | 샤드의 복제본 |

### 역인덱스 (Inverted Index) ◑

```
일반 인덱스:    문서 ID → 단어들
역인덱스:      단어 → 문서 ID 목록

예시:
"검색" → [doc1, doc3, doc7]
"엔진" → [doc1, doc5, doc7]
```

### ELK Stack ◑

| 구성 요소 | 역할 |
|-----------|------|
| **Elasticsearch** | 검색 및 저장 |
| **Logstash** | 로그 수집 및 변환 |
| **Kibana** | 시각화 대시보드 |
| **Beats** | 경량 데이터 수집기 (Filebeat, Metricbeat 등) |

---

## 4. RabbitMQ ◑

### RabbitMQ란?

AMQP 프로토콜 기반의 메시지 브로커로, 애플리케이션 간 비동기 통신에 사용된다.

### RabbitMQ 핵심 개념 ◑

```
Producer → Exchange → Queue → Consumer
              ↓
         Binding (Routing Key)
```

| 개념 | 설명 |
|------|------|
| **Exchange** | 메시지 라우팅 규칙 정의 |
| **Queue** | 메시지 저장소 |
| **Binding** | Exchange와 Queue 연결 |
| **Routing Key** | 메시지 라우팅 기준 |

### Exchange 타입 ◑

| 타입 | 라우팅 방식 |
|------|------------|
| **Direct** | 정확한 라우팅 키 매칭 |
| **Fanout** | 모든 바인딩된 큐에 브로드캐스트 |
| **Topic** | 패턴 매칭 (*.error, log.#) |
| **Headers** | 헤더 속성 기반 매칭 |

### 메시지 확인 (Acknowledgment) ◑

| 모드 | 설명 |
|------|------|
| **Auto-ack** | 전송 즉시 확인 (메시지 손실 가능) |
| **Manual-ack** | 처리 완료 후 명시적 확인 |
| **Negative-ack** | 처리 실패 시 재큐잉 또는 버림 |

---

## 5. 도구 비교 및 선택 가이드 ◑

### 메시지 브로커 비교 ◑

| 기준 | Kafka | RabbitMQ | Redis Pub/Sub |
|------|-------|----------|---------------|
| 처리량 | 매우 높음 | 높음 | 높음 |
| 내구성 | 높음 (디스크) | 높음 | 낮음 (메모리) |
| 순서 보장 | 파티션 내 | 큐 내 | 없음 |
| 메시지 재처리 | 가능 | 어려움 | 불가능 |
| 프로토콜 | 자체 프로토콜 | AMQP | Redis |
| 적합 용도 | 이벤트 스트리밍 | 작업 큐 | 실시간 알림 |

### 캐시 선택 가이드 ◑

| 상황 | 추천 |
|------|------|
| 단순 키-값 캐시 | Redis, Memcached |
| 복잡한 자료구조 필요 | Redis |
| 세션 저장 | Redis |
| 분산 락 | Redis (Redlock) |
| 로컬 캐시 (JVM) | Caffeine, Guava Cache |
| CDN/HTTP 캐시 | Varnish, Nginx |

### 검색 엔진 선택 가이드 ◑

| 상황 | 추천 |
|------|------|
| 전문 검색 | Elasticsearch, OpenSearch |
| 로그 분석 | Elasticsearch + Kibana |
| 벡터 검색 (AI) | Milvus, Pinecone, Elasticsearch |
| 간단한 검색 | PostgreSQL Full-text, Algolia |

---

## 6. 데이터베이스 보조 도구 ◑

### 연결 풀링 ◑

| 도구 | 대상 DB | 특징 |
|------|---------|------|
| **HikariCP** | JDBC | 고성능, Spring Boot 기본 |
| **PgBouncer** | PostgreSQL | 경량, 연결 풀링 전용 |
| **ProxySQL** | MySQL | 쿼리 라우팅, 페일오버 |

### 마이그레이션 도구 ◑

| 도구 | 특징 |
|------|------|
| **Flyway** | SQL 기반, 버전 관리 |
| **Liquibase** | XML/YAML/JSON 지원 |
| **Alembic** | Python (SQLAlchemy) |
| **Prisma Migrate** | TypeScript/Node.js |

---

## 7. 관측성 도구 ◑

### 메트릭 수집 ◑

| 도구 | 특징 |
|------|------|
| **Prometheus** | Pull 기반, 시계열 DB, PromQL |
| **Grafana** | 시각화 대시보드 |
| **InfluxDB** | 시계열 DB, Push 기반 |
| **Datadog** | SaaS, 통합 모니터링 |

### 분산 트레이싱 ◑

| 도구 | 특징 |
|------|------|
| **Jaeger** | Uber 개발, OpenTracing |
| **Zipkin** | Twitter 개발 |
| **OpenTelemetry** | 표준화된 계측 프레임워크 |

### 로그 수집 ◑

| 도구 | 특징 |
|------|------|
| **Fluentd/Fluent Bit** | 경량, 플러그인 풍부 |
| **Logstash** | ELK 스택 구성 요소 |
| **Vector** | Rust 기반, 고성능 |

---

## 8. 기타 인프라 도구 ◑

### API Gateway ◑

| 도구 | 특징 |
|------|------|
| **Kong** | Nginx 기반, 플러그인 풍부 |
| **AWS API Gateway** | 서버리스 통합 |
| **Nginx** | 리버스 프록시, 로드 밸런싱 |
| **Envoy** | 서비스 메시, gRPC 지원 |

### 서비스 디스커버리 ◑

| 도구 | 특징 |
|------|------|
| **Consul** | 서비스 디스커버리 + KV 저장소 |
| **etcd** | Kubernetes 기본 저장소 |
| **Zookeeper** | 분산 조정 서비스 |
| **Eureka** | Netflix OSS, Spring Cloud |

### 비밀 관리 ◑

| 도구 | 특징 |
|------|------|
| **HashiCorp Vault** | 비밀 관리, 동적 자격증명 |
| **AWS Secrets Manager** | AWS 통합 |
| **SOPS** | Git 친화적 암호화 |

---

## 면접 대비 체크리스트 ◑

- [ ] Redis 자료구조와 용도
- [ ] Redis 영속성 (RDB vs AOF)
- [ ] Redis 클러스터와 Sentinel
- [ ] Kafka 핵심 개념 (Topic, Partition, Consumer Group)
- [ ] Kafka vs RabbitMQ 차이와 선택 기준
- [ ] Kafka 메시지 전달 보장 수준
- [ ] Elasticsearch 역인덱스 원리
- [ ] ELK Stack 구성
- [ ] RabbitMQ Exchange 타입
- [ ] 메시지 브로커 선택 기준
- [ ] Prometheus + Grafana 모니터링
- [ ] 분산 트레이싱 개념 (Jaeger, Zipkin)
