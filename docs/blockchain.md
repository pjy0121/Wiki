# 16. 블록체인 (Blockchain) ◑

---

## 1. 블록체인 기초 ◑

### ⭕ 블록체인이란?

블록체인은 분산 환경에서 거래 내역을 블록 단위로 연결하여 저장하는 분산 원장(Distributed Ledger) 기술이다. 중앙 관리자 없이 P2P 네트워크의 참여자들이 데이터를 공유하고 검증한다.

기존 시스템과의 차이를 이해하면 블록체인의 가치를 알 수 있다.

```
[기존 중앙화 시스템]
         ┌──────────┐
         │  은행    │ ← 단일 장애점, 신뢰 필요
         │ (중앙DB) │
         └────┬─────┘
        ┌─────┼─────┐
        ↓     ↓     ↓
      고객A  고객B  고객C

- 은행이 모든 거래 기록 관리
- 은행을 신뢰해야 함
- 은행 서버 다운 시 전체 시스템 중단


[블록체인 (탈중앙화)]
      노드A ←──→ 노드B
        ↕   ╲   ↕
        ↕    ╲  ↕
      노드C ←──→ 노드D

- 모든 노드가 동일한 데이터 보유
- 특정 노드를 신뢰할 필요 없음 (코드를 신뢰)
- 일부 노드 다운해도 시스템 계속 작동
```

### 핵심 특징 ◑

| 특징 | 설명 | 기술적 구현 |
|------|------|------------|
| **탈중앙화** | 중앙 관리자 없이 P2P 네트워크로 운영 | P2P 네트워크, 분산 저장 |
| **불변성** | 기록된 데이터는 변경 불가 | 암호학적 해시 연결, 합의 알고리즘 |
| **투명성** | 모든 거래 내역 공개 검증 가능 | 공개 원장, 누구나 검증 가능 |
| **합의 기반** | 네트워크 참여자들의 합의로 상태 결정 | PoW, PoS 등 합의 알고리즘 |

### 블록 구조 ◑

블록은 헤더(Header)와 바디(Body)로 구성된다.

```
┌────────────────────────────────────────────────────────────┐
│                      Block Header                          │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ Version              : 블록 버전                      │  │
│  │ Previous Block Hash  : 이전 블록 헤더의 해시          │  │  ← 체인 연결!
│  │ Merkle Root          : 트랜잭션 해시 트리의 루트      │  │
│  │ Timestamp            : 블록 생성 시간                 │  │
│  │ Difficulty Target    : 채굴 난이도                    │  │
│  │ Nonce                : PoW 퍼즐 해답                  │  │
│  └──────────────────────────────────────────────────────┘  │
├────────────────────────────────────────────────────────────┤
│                      Block Body                            │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ Transaction Count    : 트랜잭션 개수                  │  │
│  │ Transactions         : 거래 목록                      │  │
│  │   - Tx1: Alice → Bob (1 BTC)                         │  │
│  │   - Tx2: Charlie → David (0.5 BTC)                   │  │
│  │   - ...                                               │  │
│  └──────────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────────┘
```

#### 블록 체인 연결 원리 ◑

각 블록의 헤더에 이전 블록 헤더의 해시가 포함되어 체인처럼 연결된다.

```
블록 100             블록 101             블록 102
┌──────────┐        ┌──────────┐        ┌──────────┐
│Prev: xyz │←───────│Prev: abc │←───────│Prev: def │
│Hash: abc │        │Hash: def │        │Hash: ghi │
│Txs: ...  │        │Txs: ...  │        │Txs: ...  │
└──────────┘        └──────────┘        └──────────┘

만약 블록 101의 내용을 변경하면:
- 블록 101의 해시가 변경됨 (def → zzz)
- 블록 102의 Prev Hash가 일치하지 않음 (def ≠ zzz)
- 체인이 끊어짐 → 변조 탐지!
```

이러한 구조 때문에 과거 블록을 변경하려면 해당 블록 이후의 모든 블록을 다시 계산해야 하며, 이는 합의 알고리즘에 의해 사실상 불가능하다.

### 블록체인 유형 ◑

| 유형 | 참여 | 읽기 | 쓰기 | 합의 | 예시 |
|------|------|------|------|------|------|
| **Public** | 누구나 | 공개 | 누구나 | PoW/PoS | Bitcoin, Ethereum |
| **Private** | 허가된 자 | 제한적 | 제한적 | PBFT, Raft | Hyperledger Fabric |
| **Consortium** | 그룹 내 | 그룹 내 | 그룹 내 | 투표/PBFT | R3 Corda, Quorum |

| 특성 | Public | Private/Consortium |
|------|--------|-------------------|
| 탈중앙화 정도 | 높음 | 낮음 |
| 처리 속도 | 느림 (수십 TPS) | 빠름 (수천 TPS) |
| 에너지 효율 | 낮음 (PoW 경우) | 높음 |
| 프라이버시 | 낮음 | 높음 |
| 신뢰 모델 | 코드/암호학 신뢰 | 컨소시엄 참여자 신뢰 |

---

## 2. 암호학적 기반 ◑

블록체인의 보안은 암호학에 기반한다. 해시 함수, 공개키 암호화, 디지털 서명이 핵심이다.

### 해시 함수 (Hash Function) ◑

해시 함수는 임의 길이의 입력을 고정 길이의 출력으로 변환하는 일방향 함수다.

```
입력                  SHA-256 해시
────────────────────────────────────────────────────────────────────
"Hello"               185f8db32271fe25f561a6fc938b2e264306ec304eda518007d1764826381969
"Hello!"              334d016f755cd6dc58c53a86e183882f8ec14f52fb05345887c8a5edd42c87b7
"Hello!!"             전혀 다른 해시 (눈사태 효과)
────────────────────────────────────────────────────────────────────
문자 하나 변경 → 완전히 다른 해시
```

| 특성 | 설명 | 블록체인에서의 역할 |
|------|------|-------------------|
| **결정성** | 같은 입력 → 항상 같은 출력 | 누구나 동일한 결과 검증 가능 |
| **빠른 계산** | 해시 생성이 빠름 | 대량의 트랜잭션 처리 가능 |
| **역상 저항성** | 해시로 원본 추측 불가 | 데이터 기밀 유지 |
| **제2 역상 저항성** | 같은 해시를 만드는 다른 입력 찾기 어려움 | 트랜잭션 위조 방지 |
| **충돌 저항성** | 같은 해시를 가진 두 입력 찾기 어려움 | 데이터 무결성 |
| **눈사태 효과** | 작은 입력 변화 → 완전히 다른 해시 | 변조 즉시 탐지 |

**주요 해시 알고리즘:**
- **SHA-256**: Bitcoin에서 사용, 256비트 출력
- **Keccak-256**: Ethereum에서 사용 (SHA-3 후보)
- **RIPEMD-160**: Bitcoin 주소 생성에 사용

### ⭕ 머클 트리 (Merkle Tree)란?

머클 트리는 트랜잭션들의 해시를 트리 구조로 요약하여 데이터 무결성을 효율적으로 검증하는 자료구조다.

```
                    Merkle Root (블록 헤더에 저장)
                           │
                 ┌─────────┴─────────┐
                 │                   │
              H(AB)               H(CD)
              /    \              /    \
           H(A)    H(B)       H(C)    H(D)
            │       │          │       │
           Tx1     Tx2        Tx3     Tx4
```

**머클 루트 계산 과정:**
```
1. 각 트랜잭션의 해시 계산
   H(Tx1) = H(A), H(Tx2) = H(B), ...

2. 인접한 해시를 쌍으로 묶어 해시
   H(AB) = Hash(H(A) + H(B))
   H(CD) = Hash(H(C) + H(D))

3. 루트에 도달할 때까지 반복
   Merkle Root = Hash(H(AB) + H(CD))
```

**머클 트리의 장점:**

1. **효율적인 검증 (SPV: Simplified Payment Verification)**
   - 전체 블록을 다운로드하지 않고도 특정 트랜잭션의 포함 여부 검증 가능
   - 필요한 데이터: 해당 트랜잭션 + 경로상의 형제 해시들 + 머클 루트

```
Tx2가 블록에 포함되어 있는지 검증:
필요한 데이터: Tx2, H(A), H(CD), Merkle Root (총 4개)

검증 과정:
1. H(B) = Hash(Tx2)
2. H(AB) = Hash(H(A) + H(B))
3. Merkle Root' = Hash(H(AB) + H(CD))
4. Merkle Root' == 블록의 Merkle Root? → 검증 완료!

전체 트랜잭션(1000개) 중 검증에 필요한 해시: log₂(1000) ≈ 10개
```

2. **변조 탐지**: 어떤 트랜잭션이라도 변경되면 머클 루트가 변경됨

### 공개키 암호화와 디지털 서명 ◑

블록체인에서 거래의 소유권과 인증은 공개키 암호화로 구현된다.

```
[키 쌍 생성]
개인키(Private Key) ───생성───→ 공개키(Public Key) ───해시───→ 주소(Address)
   (비밀)                         (공개)                       (공개)

256비트 난수         secp256k1          SHA-256 + RIPEMD-160
                     타원곡선            (Bitcoin)

[디지털 서명 과정]
                     ┌─────────────────────────────────────────┐
트랜잭션 데이터 ──→  │  Hash(Tx Data)  ──→  서명  ──→  서명된 Tx │
                     │                    ↑                     │
개인키 ──────────────│────────────────────┘                     │
                     └─────────────────────────────────────────┘

[서명 검증]
서명된 트랜잭션 ──→ 공개키로 서명 검증 ──→ 유효/무효
                    (공개키 = 트랜잭션의 보낸 사람)
```

| 개념 | 역할 | 특징 |
|------|------|------|
| **개인키** | 트랜잭션 서명 생성 | 절대 비밀로 보관, 분실 시 자산 상실 |
| **공개키** | 서명 검증 | 개인키에서 파생, 공개해도 안전 |
| **주소** | 거래 상대방 식별 | 공개키에서 해시로 파생 |
| **서명** | 트랜잭션 소유권 증명 | 개인키 없이는 생성 불가 |

**사용 알고리즘:**
- **ECDSA**: Elliptic Curve Digital Signature Algorithm
- **secp256k1**: Bitcoin, Ethereum에서 사용하는 타원곡선

---

## 3. 합의 알고리즘 ◑

### ⭕ 합의 알고리즘이란?

합의 알고리즘은 분산 네트워크에서 모든 노드가 동일한 데이터 상태에 동의하기 위한 규칙이다. 중앙 관리자 없이 "어떤 블록이 유효한가?"를 결정한다.

합의 알고리즘이 해결해야 하는 문제:
- **비잔틴 장군 문제**: 악의적인 노드가 있어도 정직한 노드들이 올바른 결정에 도달
- **이중 지불 문제**: 같은 자산을 두 번 사용하는 것 방지
- **포크 해결**: 동시에 여러 블록이 생성됐을 때 하나의 체인 선택

### 주요 합의 알고리즘 비교 ◑

| 알고리즘 | 원리 | 장점 | 단점 | TPS |
|----------|------|------|------|-----|
| **PoW** | 연산 퍼즐 해결 | 검증된 보안, 탈중앙화 | 에너지 낭비, 느림 | ~7 |
| **PoS** | 지분 기반 검증 | 에너지 효율 | Nothing at Stake | ~30 |
| **DPoS** | 투표로 대표 선출 | 빠른 합의 | 중앙화 우려 | ~1000+ |
| **PBFT** | 3f+1 노드 합의 | 즉각적 확정 | 확장성 제한 | ~1000 |
| **PoA** | 신원 기반 검증 | 고성능 | 탈중앙화 약함 | ~1000+ |

### ⭕ PoW (Proof of Work) ◑

채굴자가 특정 조건을 만족하는 nonce 값을 찾아 블록을 생성하는 방식이다.

```
[PoW 채굴 과정]

목표: Hash(Block Header) < Target (0000...로 시작하는 해시 찾기)

채굴자의 작업:
┌─────────────────────────────────────────────────────────────────┐
│ 1. 블록 헤더 구성 (Txs, Prev Hash, Timestamp 등)                 │
│ 2. Nonce = 0 부터 시작                                          │
│                                                                  │
│ while True:                                                      │
│     hash = SHA256(SHA256(Block Header + Nonce))                 │
│     if hash < Target:                                            │
│         블록 발견! 네트워크에 전파                                │
│         break                                                    │
│     else:                                                        │
│         Nonce += 1                                               │
│         계속 시도...                                             │
└─────────────────────────────────────────────────────────────────┘

예시:
Target: 0000000000000000000abc...
Nonce=0: Hash = 7f3a8b2c... (실패, 0으로 시작 안 함)
Nonce=1: Hash = 2e9f1d4a... (실패)
...
Nonce=2,849,571,234: Hash = 0000000000000000000123... (성공!)
```

**난이도 조절:**
```
Bitcoin: 2016 블록마다 난이도 조절 (약 2주)
목표: 평균 블록 생성 시간 10분 유지

최근 2016개 블록 생성 시간 < 2주 → 난이도 증가
최근 2016개 블록 생성 시간 > 2주 → 난이도 감소
```

**51% 공격:**
```
정직한 채굴자: 49%의 해시파워
악의적 채굴자: 51%의 해시파워

공격자가 할 수 있는 것:
- 이중 지불: 거래를 보내고, 더 긴 체인을 만들어 해당 거래 무효화
- 특정 트랜잭션 검열

공격자가 할 수 없는 것:
- 다른 사람의 자금 훔치기 (개인키가 없으므로 서명 불가)
- 새로운 코인 임의 생성
```

### ⭕ PoS (Proof of Stake) ◑

지분(Stake)에 비례하여 블록 생성 권한을 부여하는 방식이다. 에너지 낭비 문제를 해결한다.

```
[PoS 동작 원리]

검증자(Validator) 선출:
┌─────────────────────────────────────────┐
│ 검증자 A: 1000 ETH 스테이킹              │
│ 검증자 B: 500 ETH 스테이킹               │
│ 검증자 C: 300 ETH 스테이킹               │
│ 검증자 D: 200 ETH 스테이킹               │
└─────────────────────────────────────────┘
           ↓
      확률적 선출
           ↓
A 선출 확률: 1000/2000 = 50%
B 선출 확률: 500/2000 = 25%
C 선출 확률: 300/2000 = 15%
D 선출 확률: 200/2000 = 10%
```

| 개념 | 설명 |
|------|------|
| **Validator** | 지분을 예치(Staking)하고 블록을 검증/생성하는 노드 |
| **Staking** | 검증자가 되기 위해 자산을 예치하는 것 |
| **Slashing** | 악의적 행위(이중 서명 등) 시 예치금 일부 또는 전부 몰수 |
| **Attestation** | 블록의 유효성에 대한 투표 |

**Nothing at Stake 문제:**

PoS의 핵심 취약점으로, 포크 발생 시 검증자가 모든 포크에 투표하는 것이 유리한 문제다.

```
[Nothing at Stake]

       블록 N
          │
    ┌─────┴─────┐  ← 포크 발생!
    ↓           ↓
 체인 A      체인 B

PoW에서: 해시파워를 분산해야 함 (비용 발생)
PoS에서: 두 체인 모두에 투표 가능 (비용 없음)
        → 어느 쪽이 이기든 보상 획득
        → 체인 안정성 해침

해결책: Slashing
- 두 체인에 모두 투표한 증거 발견 시 예치금 몰수
- 잃을 것이 있으므로 한 체인만 선택하게 됨
```

**Ethereum 2.0 PoS (Gasper):**
- 최소 32 ETH 스테이킹 필요
- Casper FFG (Finality Gadget): 체크포인트 기반 확정성
- LMD GHOST: 포크 선택 규칙
- Slashing 조건: 이중 투표, 모순 투표

### DPoS (Delegated Proof of Stake) ◑

토큰 보유자가 투표로 대표(Delegate)를 선출하고, 대표들이 블록을 생성한다.

```
[DPoS 구조]

토큰 보유자 (수백만 명)
         │
         │ 투표
         ↓
┌─────────────────────────────────────┐
│ 대표 21명 (EOS) / 27명 (BNB Chain) │
│ - 블록 생성                          │
│ - 라운드 로빈 방식으로 순서대로 생성   │
└─────────────────────────────────────┘
```

장점: 매우 빠른 블록 생성 (~0.5초), 높은 TPS
단점: 소수의 대표에게 권력 집중, 탈중앙화 약화

### PBFT (Practical Byzantine Fault Tolerance) ◑

모든 노드가 서로 통신하여 합의에 도달하는 방식이다.

```
[PBFT 합의 과정]

조건: 3f+1개 노드 (f개의 악의적 노드 허용)
예: 4개 노드 → 1개 악의적 노드 허용

1. Pre-prepare: 리더가 제안
   리더 → 전체: "이 블록을 추가합시다"

2. Prepare: 노드들이 제안 수신 확인
   각 노드 → 전체: "제안을 받았습니다"
   (2f+1개 이상의 prepare 메시지 수신 시 다음 단계)

3. Commit: 합의 확정
   각 노드 → 전체: "동의합니다"
   (2f+1개 이상의 commit 메시지 수신 시 블록 확정)
```

장점: 즉각적인 확정성 (finality)
단점: O(n²) 메시지 복잡도로 노드 수 확장 어려움 (보통 100개 미만)

---

## 4. 비트코인과 이더리움 ◑

### Bitcoin vs Ethereum ◑

| 구분 | Bitcoin | Ethereum |
|------|---------|----------|
| 목적 | 디지털 화폐, 가치 저장 | 스마트 컨트랙트 플랫폼 |
| 합의 | PoW (SHA-256) | PoS (Gasper) |
| 블록 시간 | ~10분 | ~12초 |
| 블록 크기 | ~1MB (+ SegWit) | 가스 한도 기반 |
| 스크립트 | 제한적 (Bitcoin Script) | 튜링 완전 (EVM) |
| 상태 모델 | UTXO | Account |
| 총 발행량 | 2,100만 BTC (고정) | 무제한 (발행률 감소 중) |
| 스마트 컨트랙트 | 제한적 | 완전 지원 |
| 주요 언어 | - | Solidity, Vyper |

### UTXO vs Account 모델 ◑

블록체인에서 잔액을 관리하는 두 가지 다른 방식이다.

#### UTXO (Unspent Transaction Output) 모델 ◑

Bitcoin에서 사용하는 방식으로, "미사용 출력"을 추적한다.

```
[UTXO 모델의 거래]

Alice가 Bob에게 0.5 BTC 보내기
Alice의 UTXO: 1 BTC (이전 거래에서 받은 것)

┌──────────────────────────────────────────────────────────────┐
│ 트랜잭션                                                      │
│                                                              │
│  Input:                        Output:                       │
│  ┌─────────────────────┐      ┌─────────────────────┐       │
│  │ Alice의 1 BTC UTXO  │  →   │ Bob에게 0.5 BTC     │ UTXO1 │
│  │ (소비됨)            │      │ Alice에게 0.4 BTC   │ UTXO2 │
│  └─────────────────────┘      │ (거스름돈)          │       │
│                               │ 수수료 0.1 BTC      │       │
│                               └─────────────────────┘       │
└──────────────────────────────────────────────────────────────┘

거래 후:
- Alice의 1 BTC UTXO → 소비됨 (삭제)
- Bob의 0.5 BTC UTXO → 새로 생성
- Alice의 0.4 BTC UTXO → 새로 생성 (거스름돈)

Alice의 잔액 = Alice가 소유한 모든 UTXO의 합
```

UTXO 모델의 특징:
- **병렬 처리 용이**: 서로 다른 UTXO는 독립적으로 처리 가능
- **프라이버시 향상**: 매 거래마다 새 주소 사용 가능
- **검증 간단**: 해당 UTXO가 존재하고 서명이 유효한지만 확인

#### Account 모델 ◑

Ethereum에서 사용하는 방식으로, 은행 계좌처럼 잔액을 관리한다.

```
[Account 모델의 거래]

Alice: 1 ETH 보유
Bob: 0 ETH 보유

Alice가 Bob에게 0.5 ETH 보내기:

거래 전:                      거래 후:
┌────────────────────┐       ┌────────────────────┐
│ Alice: 1 ETH       │  →    │ Alice: 0.4 ETH     │
│ Bob: 0 ETH         │       │ Bob: 0.5 ETH       │
└────────────────────┘       └────────────────────┘
                             (수수료 0.1 ETH)

단순히 잔액만 변경됨
```

Account 모델의 특징:
- **직관적**: 잔액 확인이 간단 (UTXO 합산 불필요)
- **스마트 컨트랙트에 적합**: 컨트랙트도 계정으로 표현
- **상태 관리 용이**: 계정별 nonce, storage 관리

| 비교 | UTXO | Account |
|------|------|---------|
| 잔액 확인 | UTXO 합산 | 직접 조회 |
| 병렬 처리 | 용이 | 순차 처리 필요 (nonce) |
| 프라이버시 | 높음 (매번 새 주소) | 낮음 (같은 주소 재사용) |
| 스마트 컨트랙트 | 제한적 | 최적화 |
| 복잡도 | 검증 간단, 추적 복잡 | 검증 복잡, 추적 간단 |

---

## 5. 스마트 컨트랙트 ◑

### ⭕ 스마트 컨트랙트란?

스마트 컨트랙트는 블록체인에서 자동으로 실행되는 프로그램이다. 조건이 충족되면 계약 내용이 자동으로 이행되며, 중개자 없이 신뢰를 보장한다.

```
[일반 계약 vs 스마트 컨트랙트]

일반 계약 (에스크로):
구매자 ───돈───→ 에스크로 서비스 ───돈───→ 판매자
              (제3자 신뢰 필요)     ↑
                                  상품 확인 후

스마트 컨트랙트:
┌────────────────────────────────────────────┐
│ 스마트 컨트랙트 (코드가 법)                  │
│                                            │
│ if (상품_배송_확인 == true) {              │
│     판매자에게_자금_전송();                  │
│ } else if (7일_경과 && 분쟁_없음) {         │
│     판매자에게_자금_전송();                  │
│ } else if (분쟁_제기) {                    │
│     분쟁_해결_로직();                       │
│ }                                          │
└────────────────────────────────────────────┘
- 코드가 공개되어 있어 누구나 검증 가능
- 한번 배포되면 변경 불가 (불변성)
- 조건 충족 시 자동 실행
```

### EVM (Ethereum Virtual Machine) ◑

EVM은 스마트 컨트랙트를 실행하는 Ethereum의 가상 머신이다.

```
[EVM 아키텍처]

┌─────────────────────────────────────────────────────────────┐
│                    Ethereum World State                      │
│  ┌─────────────────────────────────────────────────────────┐│
│  │ Account 1 (EOA)      │ Account 2 (Contract)             ││
│  │ - Balance: 10 ETH    │ - Balance: 5 ETH                 ││
│  │ - Nonce: 5           │ - Nonce: 0                       ││
│  │                      │ - Code: 0x6080...                ││
│  │                      │ - Storage: {slot0: value0, ...}  ││
│  └─────────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────┘

[EVM 실행 환경]
┌─────────────────────────────────────────────────────────────┐
│  Stack (256비트 워드, 최대 1024개)                           │
│  [top] value3 | value2 | value1 | value0 [bottom]          │
├─────────────────────────────────────────────────────────────┤
│  Memory (바이트 배열, 휘발성)                                 │
│  [0x00] [0x01] [0x02] ... [0xFF] ...                        │
├─────────────────────────────────────────────────────────────┤
│  Storage (영구 저장소, 키-값 매핑)                            │
│  {slot0: value0, slot1: value1, ...}                        │
└─────────────────────────────────────────────────────────────┘
```

| 개념 | 설명 |
|------|------|
| **스택 기반** | 256비트 워드 스택 머신, 최대 깊이 1024 |
| **바이트코드** | Solidity → EVM 바이트코드로 컴파일 |
| **Gas** | 연산 비용 단위, 무한 루프 방지 |
| **Storage** | 영구 저장소, 가장 비싼 연산 |
| **Memory** | 휘발성 메모리, 트랜잭션 종료 시 삭제 |

### Solidity 기초 ◑

Solidity는 Ethereum 스마트 컨트랙트를 위한 고급 언어다.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

// 간단한 토큰 컨트랙트 예시
contract SimpleToken {
    // 상태 변수 (Storage에 저장)
    mapping(address => uint256) public balances;
    uint256 public totalSupply;
    address public owner;

    // 이벤트 (로그로 기록)
    event Transfer(address indexed from, address indexed to, uint256 amount);

    // 생성자 (배포 시 한 번 실행)
    constructor(uint256 _initialSupply) {
        owner = msg.sender;
        balances[msg.sender] = _initialSupply;
        totalSupply = _initialSupply;
    }

    // 접근 제어자
    modifier onlyOwner() {
        require(msg.sender == owner, "Not owner");
        _;
    }

    // 토큰 전송 함수
    function transfer(address _to, uint256 _amount) public returns (bool) {
        require(balances[msg.sender] >= _amount, "Insufficient balance");
        require(_to != address(0), "Invalid address");

        balances[msg.sender] -= _amount;
        balances[_to] += _amount;

        emit Transfer(msg.sender, _to, _amount);
        return true;
    }

    // view 함수: 상태 읽기만, 가스 무료 (외부 호출 시)
    function balanceOf(address _account) public view returns (uint256) {
        return balances[_account];
    }
}
```

| 키워드 | 설명 |
|--------|------|
| `mapping` | 키-값 저장소 (해시 테이블) |
| `public` | 외부에서 호출 가능, 자동 getter 생성 |
| `private` | 컨트랙트 내부에서만 접근 |
| `view` | 상태 변경 없음, 읽기 전용 |
| `pure` | 상태 읽기/쓰기 없음, 순수 연산 |
| `payable` | ETH 수신 가능 |
| `msg.sender` | 함수 호출자 주소 |
| `msg.value` | 전송된 ETH 양 (wei) |

### ⭕ Gas란?

Gas는 EVM에서 연산 수행에 필요한 비용 단위다. 무한 루프나 과도한 연산으로부터 네트워크를 보호한다.

```
트랜잭션 비용 계산:

Transaction Fee = Gas Used × Gas Price

예시:
- 단순 ETH 전송: 21,000 Gas
- ERC-20 토큰 전송: ~65,000 Gas
- Uniswap 스왑: ~150,000 Gas
- NFT 민팅: ~200,000 Gas

Gas Price: 30 Gwei = 0.00000003 ETH
Gas Used: 21,000
Fee = 21,000 × 0.00000003 = 0.00063 ETH ≈ $1.5 (ETH $2,400 기준)
```

**EIP-1559 가스 모델 (London 업그레이드):**

```
Transaction Fee = (Base Fee + Priority Fee) × Gas Used

┌─────────────────────────────────────────────────────────────┐
│ Base Fee: 네트워크가 자동 결정, 소각됨                        │
│   - 이전 블록이 50% 이상 찼으면 → Base Fee 증가              │
│   - 이전 블록이 50% 미만 찼으면 → Base Fee 감소              │
│                                                             │
│ Priority Fee (Tip): 사용자가 설정, 검증자에게 지급            │
│   - 급한 트랜잭션: 높은 Priority Fee                         │
│   - 여유로운 트랜잭션: 낮은 Priority Fee                     │
│                                                             │
│ Max Fee: 사용자가 지불할 최대 가격                           │
│   - 실제 사용: Base Fee + Priority Fee                       │
│   - 초과분: 환불                                             │
└─────────────────────────────────────────────────────────────┘
```

| 용어 | 설명 |
|------|------|
| **Gas Limit** | 트랜잭션/블록당 최대 Gas |
| **Gas Price** | Gas당 지불할 ETH (Gwei, 1 Gwei = 10⁻⁹ ETH) |
| **Base Fee** | 네트워크가 결정하는 최소 가격, 소각됨 |
| **Priority Fee** | 검증자에게 주는 팁 |
| **Max Fee** | 지불할 최대 총 가격 |

---

## 6. 스마트 컨트랙트 보안 ◑

스마트 컨트랙트는 배포 후 수정이 불가능하고, 금전적 가치가 있어 보안이 매우 중요하다.

### 주요 취약점 ◑

| 취약점 | 설명 | 유명한 사례 |
|--------|------|------------|
| **Reentrancy** | 외부 호출 중 재진입하여 자금 탈취 | The DAO 해킹 (2016, $60M) |
| **Integer Overflow** | 정수 연산 오버플로우 | BEC 토큰 (2018) |
| **Front-running** | 트랜잭션 순서 조작으로 이익 획득 | DEX에서 빈번 |
| **Access Control** | 권한 검사 누락 | Parity 지갑 (2017, $30M) |
| **Denial of Service** | 무한 루프, 가스 고갈 | GovernMental Ponzi |
| **Signature Replay** | 서명 재사용 공격 | 다양한 브릿지 해킹 |

### ⭕ Reentrancy 공격 ◑

가장 유명한 스마트 컨트랙트 취약점으로, The DAO 해킹의 원인이었다.

```solidity
// 취약한 컨트랙트
contract VulnerableBank {
    mapping(address => uint) public balances;

    function deposit() public payable {
        balances[msg.sender] += msg.value;
    }

    function withdraw() public {
        uint amount = balances[msg.sender];

        // 1. 외부 호출 (여기서 재진입 발생!)
        (bool success, ) = msg.sender.call{value: amount}("");
        require(success);

        // 2. 상태 변경 (너무 늦음!)
        balances[msg.sender] = 0;
    }
}

// 공격자 컨트랙트
contract Attacker {
    VulnerableBank public bank;

    constructor(address _bankAddress) {
        bank = VulnerableBank(_bankAddress);
    }

    // 공격 시작
    function attack() external payable {
        bank.deposit{value: 1 ether}();
        bank.withdraw();
    }

    // ETH 수신 시 자동 호출 (재진입!)
    receive() external payable {
        if (address(bank).balance >= 1 ether) {
            bank.withdraw();  // 다시 withdraw 호출!
        }
    }
}
```

```
[공격 흐름]

1. 공격자가 1 ETH 입금
   balances[attacker] = 1 ETH
   Bank balance = 10 ETH

2. 공격자가 withdraw() 호출
   - amount = 1 ETH
   - msg.sender.call{value: 1 ETH}() 실행
     → Attacker의 receive() 호출됨

3. receive()에서 다시 withdraw() 호출 (재진입!)
   - balances[attacker] = 1 ETH (아직 0으로 변경 안 됨!)
   - 다시 1 ETH 인출
   → 또 receive() 호출됨

4. Bank에 잔액 남아있는 동안 반복...

5. 결과: 1 ETH 입금으로 10 ETH 전부 인출!
```

### Reentrancy 예방 ◑

```solidity
// 해결책 1: Checks-Effects-Interactions 패턴
function withdraw() public {
    uint amount = balances[msg.sender];

    // 1. Checks: 조건 검사
    require(amount > 0, "No balance");

    // 2. Effects: 상태 변경 먼저!
    balances[msg.sender] = 0;

    // 3. Interactions: 외부 호출은 마지막에
    (bool success, ) = msg.sender.call{value: amount}("");
    require(success);
}

// 해결책 2: ReentrancyGuard (OpenZeppelin)
import "@openzeppelin/contracts/security/ReentrancyGuard.sol";

contract SafeBank is ReentrancyGuard {
    mapping(address => uint) public balances;

    function withdraw() public nonReentrant {  // nonReentrant 수정자
        uint amount = balances[msg.sender];
        balances[msg.sender] = 0;
        (bool success, ) = msg.sender.call{value: amount}("");
        require(success);
    }
}
```

### 보안 도구 및 베스트 프랙티스 ◑

| 도구 | 용도 | 특징 |
|------|------|------|
| **Slither** | 정적 분석 | Python 기반, 빠른 취약점 탐지 |
| **Mythril** | 심볼릭 실행 | 깊은 분석, 시간 소요 |
| **Echidna** | 퍼징 테스트 | 속성 기반 테스트 |
| **Foundry** | 테스트/퍼징 | Rust 기반, 고성능 |
| **OpenZeppelin** | 감사된 라이브러리 | 재사용 가능한 보안 컨트랙트 |

**보안 체크리스트:**
1. Checks-Effects-Interactions 패턴 적용
2. ReentrancyGuard 사용
3. 접근 제어 (onlyOwner 등)
4. 오버플로우 방지 (Solidity 0.8+ 기본 적용)
5. 외부 호출 실패 처리
6. 프론트러닝 방지 (commit-reveal 패턴)
7. 전문 감사 업체 감사

---

## 7. 토큰 표준 ◑

### ERC 토큰 표준 ◑

ERC(Ethereum Request for Comments)는 Ethereum 토큰의 표준 인터페이스다.

| 표준 | 용도 | 특징 | 예시 |
|------|------|------|------|
| **ERC-20** | 대체 가능 토큰 | 동일한 가치의 토큰 | USDT, LINK, UNI |
| **ERC-721** | NFT (대체 불가능) | 각 토큰이 고유 | CryptoPunks, BAYC |
| **ERC-1155** | 멀티 토큰 | FT + NFT 혼합 | 게임 아이템, ENS |

### ERC-20 표준 ◑

```solidity
interface IERC20 {
    // 총 발행량
    function totalSupply() external view returns (uint256);

    // 잔액 조회
    function balanceOf(address account) external view returns (uint256);

    // 토큰 전송
    function transfer(address to, uint256 amount) external returns (bool);

    // 허용량 조회
    function allowance(address owner, address spender) external view returns (uint256);

    // 전송 권한 부여 (DEX 등에서 사용)
    function approve(address spender, uint256 amount) external returns (bool);

    // 대리 전송 (approve 받은 주소가 사용)
    function transferFrom(address from, address to, uint256 amount) external returns (bool);

    // 이벤트
    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(address indexed owner, address indexed spender, uint256 value);
}
```

**approve/transferFrom 패턴:**

```
[DEX에서 토큰 스왑 과정]

1. 사용자가 DEX 컨트랙트에 approve
   User → Token: approve(DEX, 100 tokens)

2. DEX가 사용자 대신 토큰 가져옴
   DEX → Token: transferFrom(User, DEX, 100 tokens)

3. DEX가 다른 토큰 전송
   DEX → OtherToken: transfer(User, 50 tokens)
```

### ERC-721 (NFT) 표준 ◑

```solidity
interface IERC721 {
    // 잔액 (보유 NFT 개수)
    function balanceOf(address owner) external view returns (uint256);

    // 소유자 조회
    function ownerOf(uint256 tokenId) external view returns (address);

    // NFT 전송
    function transferFrom(address from, address to, uint256 tokenId) external;
    function safeTransferFrom(address from, address to, uint256 tokenId) external;

    // 권한 부여
    function approve(address to, uint256 tokenId) external;
    function setApprovalForAll(address operator, bool approved) external;

    // 메타데이터 (선택)
    function tokenURI(uint256 tokenId) external view returns (string memory);
}
```

**ERC-20 vs ERC-721:**

| 특성 | ERC-20 | ERC-721 |
|------|--------|---------|
| 대체 가능 | O (1 USDT = 1 USDT) | X (각 NFT가 고유) |
| 잔액 | 숫자 (100 토큰) | NFT 개수 |
| 전송 | amount | tokenId |
| 소수점 | 가능 (decimals) | 불가능 |

---

## 8. DeFi와 Web3 ◑

### DeFi (Decentralized Finance) ◑

DeFi는 블록체인 위에 구축된 탈중앙화 금융 서비스다.

```
[전통 금융 vs DeFi]

전통 금융:
사용자 → 은행/거래소 → 상대방
         (중개자)
         - KYC 필요
         - 영업시간 제한
         - 수수료 높음
         - 중앙 통제

DeFi:
사용자 → 스마트 컨트랙트 → 상대방
         (코드)
         - 익명 가능
         - 24/7 운영
         - 수수료 낮음
         - 탈중앙화
```

| 프로토콜 유형 | 기능 | 대표 예시 |
|--------------|------|----------|
| **DEX** | 탈중앙화 거래소 | Uniswap, Curve, dYdX |
| **Lending** | 대출/차입 | Aave, Compound, MakerDAO |
| **Stablecoin** | 가치 고정 토큰 | DAI, USDC, FRAX |
| **Derivatives** | 파생상품 | GMX, Synthetix |
| **Yield Aggregator** | 수익 최적화 | Yearn, Convex |

### ⭕ AMM (Automated Market Maker) ◑

AMM은 유동성 풀을 사용하여 자동으로 가격을 결정하는 DEX 모델이다. 전통적인 오더북 방식과 다르다.

```
[기존 오더북 방식]
매도 주문: $101에 100개, $102에 200개, ...
매수 주문: $99에 150개, $98에 100개, ...
→ 매칭 엔진이 주문 연결

[AMM (Uniswap v2)]
┌─────────────────────────────────────────┐
│          유동성 풀 (Liquidity Pool)      │
│                                         │
│   ETH: 100개        USDC: 200,000개     │
│                                         │
│   x × y = k (Constant Product Formula)  │
│   100 × 200,000 = 20,000,000 (상수)     │
│                                         │
│   가격 = y/x = 200,000/100 = $2,000/ETH │
└─────────────────────────────────────────┘
```

**스왑 과정:**

```
[1 ETH를 USDC로 스왑]

전: x=100 ETH, y=200,000 USDC, k=20,000,000

1 ETH 추가 → x'=101 ETH
y' = k/x' = 20,000,000/101 ≈ 198,020 USDC

받는 USDC = 200,000 - 198,020 = 1,980 USDC
(수수료 제외, 실제 가격 $1,980)

슬리피지: 예상 $2,000 - 실제 $1,980 = $20 (1%)
→ 큰 거래일수록 슬리피지 증가
```

**비영구적 손실 (Impermanent Loss):**

유동성 제공자(LP)가 가격 변동으로 인해 겪는 손실이다.

```
[비영구적 손실 예시]

초기 예치: 1 ETH ($2,000) + 2,000 USDC = $4,000
ETH 가격 2배 상승: $4,000

단순 보유했다면:
1 ETH ($4,000) + 2,000 USDC = $6,000

LP로 예치했다면:
풀 균형: x × y = k
새 가격에서: 약 0.707 ETH + 2,828 USDC ≈ $5,656

비영구적 손실: $6,000 - $5,656 = $344 (5.7%)

"비영구적"인 이유: 가격이 원래대로 돌아오면 손실 사라짐
```

### Web3 개발 도구 ◑

| 도구 | 용도 | 특징 |
|------|------|------|
| **Hardhat** | 개발/테스트/배포 | JavaScript, 플러그인 풍부 |
| **Foundry** | 개발/테스트/배포 | Rust 기반, 매우 빠름 |
| **ethers.js** | 프론트엔드 연동 | 현대적 API, TypeScript |
| **viem** | 프론트엔드 연동 | TypeScript 최적화, 경량 |
| **Remix** | 브라우저 IDE | 빠른 프로토타이핑 |
| **Metamask** | 브라우저 지갑 | 가장 널리 사용 |
| **The Graph** | 온체인 데이터 인덱싱 | GraphQL API |

---

## 9. 레이어 2 솔루션 ◑

### ⭕ Layer 2란?

Layer 2는 메인넷(Layer 1)의 확장성 문제를 해결하기 위한 오프체인 솔루션이다. L1의 보안을 상속하면서 처리량을 높인다.

```
[확장성 트릴레마]
        탈중앙화
           △
          ╱ ╲
         ╱   ╲
        ╱     ╲
   보안 ─────── 확장성

세 가지를 모두 만족하기 어려움
→ L2로 확장성 문제 해결
```

### 주요 L2 솔루션 ◑

```
[Layer 2 분류]

                    Layer 2 솔루션
                         │
        ┌────────────────┼────────────────┐
        │                │                │
   State Channel      Plasma          Rollup
   (결제 채널)        (사이드체인)     (압축 저장)
        │                │                │
   Lightning        OMG Network     ┌─────┴─────┐
   Network          (종료)         │           │
                               Optimistic    ZK
                                 │           │
                            Optimism    zkSync
                            Arbitrum    StarkNet
                            Base        Polygon zkEVM
```

| 유형 | 원리 | 장점 | 단점 |
|------|------|------|------|
| **State Channel** | 오프체인 거래, 최종 상태만 기록 | 즉각적, 매우 저렴 | 특정 참여자 간만 |
| **Plasma** | 하위 체인에서 처리 | 높은 처리량 | 복잡한 탈출, 데이터 가용성 |
| **Optimistic Rollup** | 사기 증명 기반 | EVM 호환 쉬움 | 긴 출금 시간 |
| **ZK Rollup** | 영지식 증명 기반 | 빠른 확정, 높은 보안 | 복잡한 구현 |

### ⭕ Rollup 상세 ◑

Rollup은 트랜잭션을 L2에서 실행하고, 압축된 데이터를 L1에 저장하는 방식이다.

```
[Rollup 동작 원리]

Layer 2 (Rollup)                      Layer 1 (Ethereum)
┌─────────────────────┐               ┌─────────────────────┐
│ 트랜잭션 실행        │               │                     │
│ Tx1, Tx2, ..., Tx1000│               │                     │
│                     │  압축 데이터   │ calldata로 저장      │
│ 배치로 묶음         │ ────────────→  │ (저렴한 가스)        │
│                     │               │                     │
│ 새 상태 계산        │  상태 루트     │ 상태 업데이트        │
│                     │ ────────────→  │ (검증 후)           │
└─────────────────────┘               └─────────────────────┘

가스 비용 비교:
L1 직접: 21,000 gas × 1000 tx = 21,000,000 gas
Rollup: ~100,000 gas (배치 전체)
→ 약 200배 저렴
```

### Optimistic vs ZK Rollup ◑

```
[Optimistic Rollup]
"낙관적으로 유효하다고 가정"

┌─────────────────────────────────────────────────────────────┐
│ 1. 시퀀서가 배치 제출 (L1에 데이터 저장)                      │
│    "이 트랜잭션들은 유효합니다"                              │
│                                                             │
│ 2. 챌린지 기간 (7일)                                        │
│    - 아무도 이의 제기 안 하면 → 확정                         │
│    - 사기 증명 제출되면 → 배치 무효화, 제출자 슬래싱         │
│                                                             │
│ 3. 출금: 7일 대기 필요 (챌린지 기간)                         │
└─────────────────────────────────────────────────────────────┘


[ZK Rollup]
"수학적 증명으로 유효성 검증"

┌─────────────────────────────────────────────────────────────┐
│ 1. 배치 실행 + 영지식 증명 생성                              │
│    - 모든 트랜잭션이 유효함을 증명하는 수학적 증명            │
│    - 증명 생성에 시간/비용 소요                              │
│                                                             │
│ 2. L1에 증명 제출 및 검증                                   │
│    - 증명이 유효하면 즉시 확정                               │
│    - 검증은 빠르고 저렴                                      │
│                                                             │
│ 3. 출금: 증명 검증 후 즉시 (몇 분)                          │
└─────────────────────────────────────────────────────────────┘
```

| 구분 | Optimistic Rollup | ZK Rollup |
|------|-------------------|-----------|
| 검증 방식 | 사기 증명 (이의 제기 시) | 영지식 증명 (항상) |
| 확정 시간 | ~7일 | 몇 분 ~ 몇 시간 |
| 출금 시간 | ~7일 | 빠름 |
| EVM 호환 | 쉬움 | 어려움 (zkEVM 개발 중) |
| 가스 비용 | 중간 | 낮음 (데이터 압축) |
| 기술 성숙도 | 높음 | 발전 중 |
| 대표 프로젝트 | Optimism, Arbitrum, Base | zkSync, StarkNet, Polygon zkEVM |

---

## 10. 기타 블록체인 플랫폼 ◑

### 주요 플랫폼 비교 ◑

| 플랫폼 | 합의 | TPS | 특징 | 생태계 |
|--------|------|-----|------|--------|
| **Ethereum** | PoS | ~30 | 최대 생태계, 스마트 컨트랙트 원조 | 가장 큼 |
| **Solana** | PoH+PoS | ~65,000 | 고성능, 저비용, 단일 레이어 | 급성장 |
| **BNB Chain** | PoSA | ~2,000 | 바이낸스 생태계, EVM 호환 | 대형 |
| **Avalanche** | Snowball | ~4,500 | 서브넷, 빠른 확정 | 성장 중 |
| **Polygon** | PoS | ~7,000 | Ethereum 사이드체인/L2 | 대형 |
| **Polkadot** | NPoS | ~1,500 | 파라체인, 크로스체인 | 성장 중 |
| **Cosmos** | Tendermint | ~10,000 | IBC, 앱 특화 체인 | 성장 중 |

### 크로스체인 기술 ◑

서로 다른 블록체인 간 자산/정보 전송을 가능하게 하는 기술이다.

```
[브릿지 (Bridge)]

Ethereum ←───────→ Bridge Contract ←───────→ Polygon
  ETH                (락업/민팅)              Wrapped ETH

1. Ethereum에서 ETH 락업
2. Polygon에서 동일 양의 WETH 민팅
3. 반대 방향: WETH 소각 → ETH 언락

보안 위험: 브릿지 해킹 ($1B+ 피해)
```

| 기술 | 원리 | 예시 |
|------|------|------|
| **Bridge** | 락업-민팅 방식 | Polygon Bridge, Wormhole |
| **IBC** | Cosmos 체인 간 통신 | Osmosis, Terra (구) |
| **XCM** | Polkadot 파라체인 간 | Acala, Moonbeam |
| **Atomic Swap** | 해시 타임락 | THORChain |

---

## 면접 대비 체크리스트 ◑

- [ ] 블록체인의 핵심 특징 (탈중앙화, 불변성, 투명성)
- [ ] 블록 구조와 체인 연결 원리 (이전 해시 포함)
- [ ] 머클 트리의 역할과 SPV 검증
- [ ] 합의 알고리즘 비교 (PoW, PoS, DPoS, PBFT)
- [ ] PoW: 51% 공격, 난이도 조절
- [ ] PoS: Nothing at Stake 문제와 Slashing
- [ ] Bitcoin vs Ethereum 차이 (목적, 합의, 상태 모델)
- [ ] UTXO vs Account 모델 차이
- [ ] 스마트 컨트랙트의 특징과 EVM
- [ ] Gas 개념과 EIP-1559 가스 모델
- [ ] Reentrancy 공격과 방어 (CEI 패턴, ReentrancyGuard)
- [ ] ERC-20, ERC-721 토큰 표준의 차이
- [ ] DeFi 기본 개념 (DEX, Lending, Stablecoin)
- [ ] AMM 원리 (x×y=k)와 비영구적 손실
- [ ] Layer 2 솔루션 (Optimistic Rollup vs ZK Rollup)
- [ ] Rollup의 확정성과 출금 시간 차이
