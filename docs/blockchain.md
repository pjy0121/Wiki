# 16. 블록체인 (Blockchain) ◑

---

## 1. 블록체인 기초 ◑

### ⭕ 블록체인이란?

분산 환경에서 거래 내역을 블록 단위로 연결하여 저장하는 분산 원장 기술이다.

### 핵심 특징 ◑

| 특징 | 설명 |
|------|------|
| **탈중앙화** | 중앙 관리자 없이 P2P 네트워크로 운영 |
| **불변성** | 기록된 데이터는 변경 불가 (암호학적 연결) |
| **투명성** | 모든 거래 내역 공개 검증 가능 |
| **합의 기반** | 네트워크 참여자들의 합의로 상태 결정 |

### 블록 구조 ◑

```
┌─────────────────────────────┐
│          Block Header       │
│  ┌───────────────────────┐  │
│  │ Previous Block Hash   │  │  ← 이전 블록과 연결
│  │ Timestamp             │  │
│  │ Nonce                 │  │  ← PoW에서 사용
│  │ Merkle Root           │  │  ← 트랜잭션 해시 트리
│  └───────────────────────┘  │
├─────────────────────────────┤
│       Transactions          │
│  Tx1, Tx2, Tx3, ...         │
└─────────────────────────────┘
```

### 블록체인 유형 ◑

| 유형 | 참여 | 읽기 | 쓰기 | 예시 |
|------|------|------|------|------|
| **Public** | 누구나 | 공개 | 누구나 | Bitcoin, Ethereum |
| **Private** | 허가된 자 | 제한적 | 제한적 | Hyperledger Fabric |
| **Consortium** | 그룹 내 | 그룹 내 | 그룹 내 | R3 Corda |

---

## 2. 암호학적 기반 ◑

### 해시 함수 (Hash Function) ◑

| 특성 | 설명 |
|------|------|
| **결정성** | 같은 입력 → 같은 출력 |
| **빠른 계산** | 효율적인 해시 생성 |
| **역상 저항성** | 해시로 원본 추측 불가 |
| **충돌 저항성** | 같은 해시 생성하는 두 입력 찾기 어려움 |
| **눈사태 효과** | 작은 입력 변화 → 완전히 다른 해시 |

**주요 해시 알고리즘:**
- SHA-256 (Bitcoin)
- Keccak-256 (Ethereum)

### ⭕ 머클 트리 (Merkle Tree)란?

트랜잭션들의 해시를 트리 구조로 요약하여 데이터 무결성을 효율적으로 검증한다.

```
        Merkle Root
           /    \
      H(AB)      H(CD)
      /  \        /  \
   H(A)  H(B)  H(C)  H(D)
    |     |     |     |
   Tx1   Tx2   Tx3   Tx4
```

**장점:**
- 전체 데이터 없이 특정 트랜잭션 검증 가능 (SPV)
- 변조 시 루트 해시가 변경되어 즉시 탐지

### 공개키 암호화 ◑

| 개념 | 설명 |
|------|------|
| **개인키** | 비밀로 보관, 서명 생성 |
| **공개키** | 공개, 서명 검증 |
| **주소** | 공개키에서 파생 (해시) |
| **서명** | 트랜잭션 소유권 증명 |

**사용 알고리즘:**
- ECDSA (Elliptic Curve Digital Signature Algorithm)
- secp256k1 (Bitcoin, Ethereum)

---

## 3. 합의 알고리즘 ◑

### ⭕ 합의 알고리즘이란?

분산 네트워크에서 모든 노드가 동일한 상태에 동의하기 위한 규칙이다.

### 주요 합의 알고리즘 ◑

| 알고리즘 | 원리 | 장점 | 단점 |
|----------|------|------|------|
| **PoW** (Proof of Work) | 연산 퍼즐 해결 | 검증된 보안 | 에너지 소비, 느림 |
| **PoS** (Proof of Stake) | 지분 기반 검증 | 에너지 효율 | 부익부 빈익빈 |
| **DPoS** (Delegated PoS) | 투표로 대표 선출 | 빠른 합의 | 중앙화 우려 |
| **PBFT** | 3f+1 노드 합의 | 즉각적 확정 | 확장성 제한 |
| **PoA** (Proof of Authority) | 신원 기반 검증 | 고성능 | 탈중앙화 약함 |

### ⭕ PoW (Proof of Work) ◑

채굴자가 특정 조건을 만족하는 nonce 값을 찾아 블록을 생성한다.

```
Hash(Block Header + Nonce) < Target Difficulty
```

| 특징 | 설명 |
|------|------|
| 난이도 조절 | 블록 생성 시간 일정 유지 (Bitcoin: ~10분) |
| 51% 공격 | 과반 해시 파워로 이중 지불 가능 |
| 에너지 비용 | 경쟁적 채굴로 막대한 전력 소비 |

### ⭕ PoS (Proof of Stake) ◑

보유 지분에 비례하여 블록 생성 권한을 부여한다.

| 구분 | 설명 |
|------|------|
| **Validator** | 지분을 예치(Staking)한 검증자 |
| **Slashing** | 악의적 행위 시 예치금 몰수 |
| **Nothing at Stake** | PoS의 보안 문제, 포크 시 모두 지지 가능 |

**Ethereum 2.0 (PoS):**
- 최소 32 ETH 스테이킹
- Casper FFG + LMD GHOST 합의

---

## 4. 비트코인과 이더리움 ◑

### Bitcoin vs Ethereum ◑

| 구분 | Bitcoin | Ethereum |
|------|---------|----------|
| 목적 | 디지털 화폐 | 스마트 컨트랙트 플랫폼 |
| 합의 | PoW | PoS (Ethereum 2.0) |
| 블록 시간 | ~10분 | ~12초 |
| 스크립트 | 제한적 (Bitcoin Script) | 튜링 완전 (EVM) |
| 언어 | - | Solidity, Vyper |

### UTXO vs Account 모델 ◑

| 모델 | 설명 | 사용 |
|------|------|------|
| **UTXO** | 미사용 출력 기반, 개별 코인 추적 | Bitcoin |
| **Account** | 계좌 잔액 기반 | Ethereum |

**UTXO (Unspent Transaction Output):**
```
Input: 이전 거래의 UTXO 참조
Output: 새로운 UTXO 생성
잔액 = 내가 소유한 UTXO의 합
```

---

## 5. 스마트 컨트랙트 ◑

### ⭕ 스마트 컨트랙트란?

블록체인에서 자동으로 실행되는 프로그램으로, 조건 충족 시 계약 내용이 자동 이행된다.

### 특징 ◑

| 특징 | 설명 |
|------|------|
| **자동 실행** | 조건 충족 시 자동 이행 |
| **불변성** | 배포 후 수정 불가 |
| **투명성** | 코드 공개, 누구나 검증 가능 |
| **신뢰 최소화** | 중개자 없이 계약 이행 |

### EVM (Ethereum Virtual Machine) ◑

| 개념 | 설명 |
|------|------|
| **스택 기반** | 256비트 스택 머신 |
| **바이트코드** | Solidity → EVM 바이트코드 컴파일 |
| **Gas** | 연산 비용 단위 |
| **State** | 계정 상태 (잔액, 코드, 스토리지) |

### Solidity 기초 ◑

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract SimpleStorage {
    uint256 private value;

    event ValueChanged(uint256 newValue);

    function set(uint256 _value) public {
        value = _value;
        emit ValueChanged(_value);
    }

    function get() public view returns (uint256) {
        return value;
    }
}
```

### ⭕ Gas란?

EVM에서 연산 수행에 필요한 비용 단위이다.

```
트랜잭션 비용 = Gas Used × Gas Price (Gwei)
```

| 용어 | 설명 |
|------|------|
| **Gas Limit** | 트랜잭션당 최대 Gas |
| **Gas Price** | Gas당 지불할 ETH (Gwei) |
| **Base Fee** | 네트워크가 결정하는 최소 가격 (EIP-1559) |
| **Priority Fee** | 검증자에게 주는 팁 |

---

## 6. 스마트 컨트랙트 보안 ◑

### 주요 취약점 ◑

| 취약점 | 설명 |
|--------|------|
| **Reentrancy** | 외부 호출 중 재진입하여 자금 탈취 (The DAO 해킹) |
| **Integer Overflow** | 정수 연산 오버플로우 (0.8.0부터 기본 체크) |
| **Front-running** | 트랜잭션 순서 조작으로 이익 획득 |
| **Access Control** | 권한 검사 누락 |
| **Denial of Service** | 무한 루프, 가스 고갈 |

### Reentrancy 예방 ◑

```solidity
// 취약한 코드
function withdraw() public {
    uint amount = balances[msg.sender];
    (bool success, ) = msg.sender.call{value: amount}("");
    require(success);
    balances[msg.sender] = 0;  // 상태 변경이 외부 호출 후
}

// 안전한 코드 (Checks-Effects-Interactions 패턴)
function withdraw() public {
    uint amount = balances[msg.sender];
    balances[msg.sender] = 0;  // 상태 변경 먼저
    (bool success, ) = msg.sender.call{value: amount}("");
    require(success);
}
```

### 보안 도구 ◑

| 도구 | 용도 |
|------|------|
| **Slither** | 정적 분석 |
| **Mythril** | 심볼릭 실행 |
| **Echidna** | 퍼징 테스트 |
| **OpenZeppelin** | 감사된 컨트랙트 라이브러리 |

---

## 7. 토큰 표준 ◑

### ERC 토큰 표준 ◑

| 표준 | 용도 | 예시 |
|------|------|------|
| **ERC-20** | 대체 가능 토큰 (Fungible) | USDT, LINK |
| **ERC-721** | 대체 불가능 토큰 (NFT) | CryptoPunks, BAYC |
| **ERC-1155** | 멀티 토큰 (FT + NFT) | 게임 아이템 |

### ERC-20 주요 함수 ◑

```solidity
interface IERC20 {
    function totalSupply() external view returns (uint256);
    function balanceOf(address account) external view returns (uint256);
    function transfer(address to, uint256 amount) external returns (bool);
    function approve(address spender, uint256 amount) external returns (bool);
    function transferFrom(address from, address to, uint256 amount) external returns (bool);
}
```

---

## 8. DeFi와 Web3 ◑

### DeFi (Decentralized Finance) ◑

| 프로토콜 | 기능 |
|----------|------|
| **DEX** | 탈중앙화 거래소 (Uniswap, Sushiswap) |
| **Lending** | 대출/차입 (Aave, Compound) |
| **Stablecoin** | 가치 고정 토큰 (DAI, USDC) |
| **Yield Farming** | 유동성 제공으로 수익 획득 |

### AMM (Automated Market Maker) ◑

유동성 풀을 사용하여 자동으로 가격을 결정하는 DEX 모델이다.

```
x × y = k (Constant Product Formula)

x: 토큰 A 수량
y: 토큰 B 수량
k: 상수 (유동성)
```

### Web3 개발 도구 ◑

| 도구 | 용도 |
|------|------|
| **Hardhat** | 개발 환경, 테스트, 배포 |
| **Foundry** | Rust 기반 고성능 툴킷 |
| **ethers.js** | JavaScript 라이브러리 |
| **web3.js** | JavaScript 라이브러리 (레거시) |
| **Remix** | 브라우저 IDE |
| **Metamask** | 브라우저 지갑 |

---

## 9. 레이어 2 솔루션 ◑

### ⭕ Layer 2란?

메인넷(Layer 1)의 확장성 문제를 해결하기 위한 오프체인 솔루션이다.

### 주요 L2 솔루션 ◑

| 유형 | 원리 | 예시 |
|------|------|------|
| **State Channel** | 오프체인 거래 후 최종 상태만 기록 | Lightning Network |
| **Plasma** | 하위 체인에서 처리, 주기적 커밋 | OMG Network |
| **Rollup (Optimistic)** | 사기 증명 기반 | Optimism, Arbitrum |
| **Rollup (ZK)** | 영지식 증명 기반 | zkSync, StarkNet |

### Optimistic vs ZK Rollup ◑

| 구분 | Optimistic Rollup | ZK Rollup |
|------|-------------------|-----------|
| 검증 | 사기 증명 (이의 제기) | 영지식 증명 (즉시) |
| 출금 | 느림 (~7일) | 빠름 |
| EVM 호환 | 쉬움 | 어려움 (개선 중) |
| 가스 비용 | 중간 | 낮음 |

---

## 10. 기타 블록체인 플랫폼 ◑

### 주요 플랫폼 비교 ◑

| 플랫폼 | 특징 | 합의 |
|--------|------|------|
| **Solana** | 고성능, 저비용 | PoH + PoS |
| **Polkadot** | 크로스체인, 파라체인 | NPoS |
| **Cosmos** | Inter-Blockchain Communication | Tendermint (BFT) |
| **Avalanche** | 서브넷, 고속 확정 | Snowball |
| **BNB Chain** | 바이낸스 생태계 | PoSA |

---

## 면접 대비 체크리스트 ◑

- [ ] 블록체인의 핵심 특징 (탈중앙화, 불변성, 투명성)
- [ ] 블록 구조와 머클 트리
- [ ] 합의 알고리즘 (PoW, PoS, DPoS, PBFT)
- [ ] PoW vs PoS 장단점
- [ ] Bitcoin vs Ethereum 차이
- [ ] UTXO vs Account 모델
- [ ] 스마트 컨트랙트와 EVM
- [ ] Gas 개념과 계산
- [ ] 주요 스마트 컨트랙트 취약점 (Reentrancy)
- [ ] ERC-20, ERC-721 토큰 표준
- [ ] DeFi 기본 개념 (DEX, AMM)
- [ ] Layer 2 솔루션 (Rollup)
