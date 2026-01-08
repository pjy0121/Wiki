# 06. 데이터베이스 (Database)

---

## 1. 데이터베이스 기본 개념

### DBMS (Database Management System)

데이터베이스를 관리하는 시스템 소프트웨어

**주요 기능:**
- 데이터 정의 (DDL)
- 데이터 조작 (DML)
- 데이터 제어 (DCL)
- 동시성 제어
- 백업 및 복구

---

## 2. 관계형 데이터베이스

### 기본 용어

| 용어 | 설명 |
|------|------|
| 테이블 (릴레이션) | 행과 열로 구성된 데이터 집합 |
| 행 (튜플, 레코드) | 하나의 데이터 항목 |
| 열 (속성, 필드) | 데이터의 속성 |
| 기본키 (PK) | 튜플을 고유하게 식별하는 속성 |
| 외래키 (FK) | 다른 테이블의 기본키를 참조 |

---

## 3. SQL

### DDL (Data Definition Language)

```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(255) UNIQUE
);

ALTER TABLE users ADD COLUMN age INT;
DROP TABLE users;
```

### DML (Data Manipulation Language)

```sql
SELECT * FROM users WHERE age > 20;
INSERT INTO users (id, name) VALUES (1, 'Kim');
UPDATE users SET name = 'Lee' WHERE id = 1;
DELETE FROM users WHERE id = 1;
```

### JOIN

| 종류 | 설명 |
|------|------|
| INNER JOIN | 양쪽 모두 일치하는 행만 |
| LEFT JOIN | 왼쪽 테이블 전체 + 일치하는 오른쪽 |
| RIGHT JOIN | 오른쪽 테이블 전체 + 일치하는 왼쪽 |
| FULL OUTER JOIN | 양쪽 모두 포함 |

---

## 4. 인덱싱

### ⭕ 인덱스란 무엇이고, 장단점은?

데이터 검색 속도를 향상시키는 자료구조

**장점:**
- 검색 속도 향상 (O(n) → O(log n))

**단점:**
- 추가 저장 공간 필요
- INSERT, UPDATE, DELETE 성능 저하

### 인덱스 자료구조

| 구조 | 특징 | 용도 |
|------|------|------|
| B-Tree | 균형 트리, 범위 검색 가능 | 일반적인 인덱스 |
| B+Tree | 리프 노드에만 데이터, 순차 접근 효율적 | RDBMS 기본 |
| Hash | O(1) 검색, 범위 검색 불가 | 동등 비교만 필요한 경우 |

### 클러스터드 vs 논클러스터드 인덱스

| 구분 | 클러스터드 | 논클러스터드 |
|------|-----------|-------------|
| 데이터 정렬 | 인덱스 순서로 정렬 | 별도 저장 |
| 테이블당 개수 | 1개만 가능 | 여러 개 가능 |
| 속도 | 범위 검색 빠름 | 단일 검색 빠름 |

---

## 5. 트랜잭션

### ⭕ ACID를 설명하라

| 속성 | 설명 |
|------|------|
| **A**tomicity (원자성) | 전부 성공 또는 전부 실패 |
| **C**onsistency (일관성) | 트랜잭션 전후 데이터 일관성 유지 |
| **I**solation (격리성) | 동시 트랜잭션 간 간섭 없음 |
| **D**urability (지속성) | 완료된 트랜잭션은 영구 저장 |

### 트랜잭션 격리 수준

| 수준 | Dirty Read | Non-Repeatable Read | Phantom Read |
|------|------------|---------------------|--------------|
| READ UNCOMMITTED | O | O | O |
| READ COMMITTED | X | O | O |
| REPEATABLE READ | X | X | O |
| SERIALIZABLE | X | X | X |

```
Dirty Read: 커밋되지 않은 데이터를 읽음
  T1: UPDATE balance = 0 (아직 커밋 안 함)
  T2: SELECT balance → 0 반환
  T1: ROLLBACK → 실제 값은 원래대로

Non-Repeatable Read: 같은 쿼리가 다른 결과 반환
  T1: SELECT balance → 1000
  T2: UPDATE balance = 500; COMMIT
  T1: SELECT balance → 500 (값이 바뀜!)

Phantom Read: 같은 조건인데 행 개수가 달라짐
  T1: SELECT COUNT(*) WHERE age > 20 → 5개
  T2: INSERT INTO users (age=25); COMMIT
  T1: SELECT COUNT(*) WHERE age > 20 → 6개 (새 행 출현!)
```

### ⭕ 데이터베이스 락(Lock)이란? ◑

동시성 제어를 위해 데이터 접근을 제한하는 메커니즘이다.

| 락 유형 | 설명 |
|---------|------|
| **Shared Lock (S)** | 읽기 락, 여러 트랜잭션이 동시 읽기 가능 |
| **Exclusive Lock (X)** | 쓰기 락, 단독 접근만 허용 |

### ⭕ 낙관적 락과 비관적 락의 차이는? ◑

| 구분 | 낙관적 (Optimistic) | 비관적 (Pessimistic) |
|------|---------------------|---------------------|
| 가정 | 충돌이 거의 없을 것 | 충돌이 자주 발생할 것 |
| 방식 | 버전/타임스탬프로 커밋 시 충돌 검사 | 조회 시 락 획득 |
| 충돌 시 | 롤백 후 재시도 | 락 해제 대기 |
| 적합한 경우 | 읽기 위주, 충돌 적음 | 쓰기 위주, 충돌 많음 |

**SQL 예시:** `SELECT ... FOR UPDATE` (비관적 락)

---

## 6. 정규화

### ⭕ 정규화란 무엇인가?

데이터 중복을 줄이고 무결성을 유지하기 위해 테이블을 분해하는 과정

| 정규형 | 조건 |
|--------|------|
| 1NF | 원자값만 포함 (다중값 속성 제거) |
| 2NF | 1NF + 부분 함수 종속 제거 |
| 3NF | 2NF + 이행 함수 종속 제거 |
| BCNF | 모든 결정자가 후보키 |

### 반정규화

성능 향상을 위해 의도적으로 정규화 규칙을 위반

---

## 7. NoSQL

### ⭕ SQL과 NoSQL의 차이는?

| 구분 | SQL | NoSQL |
|------|-----|-------|
| 스키마 | 고정 스키마 | 유연한 스키마 |
| 확장성 | 수직적 확장 | 수평적 확장 |
| 관계 | JOIN 지원 | 제한적 |
| 트랜잭션 | ACID 보장 | BASE (보통) |
| 적합한 경우 | 복잡한 쿼리, 정형 데이터 | 대용량, 비정형 데이터 |

### NoSQL 유형

| 유형 | 특징 | 예시 |
|------|------|------|
| Key-Value | 단순 키-값 저장 | Redis, DynamoDB |
| Document | JSON 형태 문서 저장 | MongoDB, CouchDB |
| Column-Family | 컬럼 기반 저장 | Cassandra, HBase |
| Graph | 노드-엣지 관계 | Neo4j |

---

## 면접 대비 체크리스트 ◑

- [ ] 기본키, 외래키 개념
- [ ] JOIN 종류 (INNER, LEFT, RIGHT, FULL)
- [ ] 인덱스 장단점과 자료구조 (B-Tree, B+Tree)
- [ ] 클러스터드 vs 논클러스터드 인덱스
- [ ] ACID 속성
- [ ] 트랜잭션 격리 수준과 문제 (Dirty Read, Phantom Read 등)
- [ ] Shared Lock vs Exclusive Lock
- [ ] 낙관적 락 vs 비관적 락
- [ ] 정규화 (1NF ~ BCNF)
- [ ] SQL vs NoSQL 차이
