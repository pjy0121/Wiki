# Claude 작업 지침

이 문서는 컴퓨터 공학 교과서 프로젝트의 맥락과 작성 지침을 담고 있다.

---

## 1. 프로젝트 개요

### 목적
- 취업/면접 준비를 위한 컴퓨터 공학 핵심 개념 정리
- 기존 GitHub Wiki (https://github.com/pjy0121/Wiki/wiki)의 내용을 체계화하고 확장

### 원본 소스
기존 Wiki는 약 49개 페이지로 구성되어 있었으며, 주요 내용은 다음과 같다:

#### 이슈 정리 페이지 (6개)
- SW 아키텍처 이슈들
- 네트워크 이슈들
- 운영체제 이슈들
- 컴퓨터 구조 이슈들
- 클라우드 이슈들
- 소프트웨어 공학 이슈들

#### C++ 심화 (19개)
1. namespace
2. overloading
3. ambiguity
4. references
5. range-based for
6. inline
7. template
8. 생성자와 소멸자
9. 동적 할당
10. 상속
11. 가상 함수
12. Make
13. CMake
14. 함수 포인터
15. 스마트 포인터
16. 타입 캐스팅
17. 전처리기
18. STL
19. Assertions

#### 웹 프로그래밍
- Node.js
- Markdown (GFM)

#### 매거진 아티클 (6개)
- Netflix와 분산 데이터베이스
- ChatGPT/LLM과 Transformer
- OSI 7계층
- 운영체제 커널
- CPU 스케줄링
- 데이터베이스 인덱싱

#### 기타
- React, Spring Framework, Swift, Kubernetes, MongoDB, Python, TensorFlow
- 디자인 패턴, 알고리즘, 용어집 등

---

## 2. 작성 스타일 지침

### 어투
- **평어체 사용** ("~이다", "~한다")
- 존댓말 사용 금지
- 간결하고 직접적인 문체

### ⭕ 표시 사용법
- ⭕ 표시는 **면접 빈출 질문/이슈** 형태로만 사용
- 일반적인 제목에는 사용하지 않음
- 질문 형태로 작성: "⭕ TCP와 UDP의 차이는?"

```markdown
# 올바른 사용
### ⭕ 프로세스와 스레드의 차이는?

# 잘못된 사용
### ⭕ 프로세스 관리
```

### 문서 구조
- 각 챕터는 폴더로 구분 (예: `01-computer-architecture/`)
- 각 폴더에 `README.md` 파일로 내용 작성
- 표를 적극 활용하여 비교 정리
- 코드 예시는 실용적인 것만 포함

### 내용 작성 원칙
- 핵심 개념을 간결하게 정리
- 불필요한 설명 제거
- 의미 있는 내용만 포함 (빈 섹션은 삭제하거나 "작성 필요"로 표시)
- 면접에서 실제로 물어볼 만한 내용 중심

### ◑ 표시 사용법 (Claude 작성 구분)
- **Claude가 새로 작성한 섹션**의 제목 뒤에 ◑ 기호를 추가
- 사용자(원저자)가 작성한 부분과 구분하기 위함
- 예시: `### 배열 (Array) ◑`

```markdown
# 사용자가 작성한 섹션 (표시 없음)
### 페이징 (Paging)

# Claude가 새로 작성한 섹션
### 배열 (Array) ◑
```

### 신뢰성 원칙
- **온라인 검색을 적극 활용**하여 내용의 정확성 검증
- 신뢰할 수 있는 출처의 내용만 포함
- 불확실한 내용은 포함하지 않음
- 급하게 작성하지 않고 **단계적으로 차근차근 진행**

---

## 3. 교과서 구조 (2025년 1월 개정)

```
cs-textbook/
├── README.md                         # 전체 목차
├── CLAUDE.md                         # 이 문서 (작업 지침)
│
├── cs-fundamentals.md                # CS 기초
├── computer-architecture.md          # 컴퓨터 구조
├── operating-system.md               # 운영체제
├── data-structures-algorithms.md     # 자료구조와 알고리즘
├── linux.md                          # Linux/Unix 기초
├── network.md                        # 네트워크
├── database.md                       # 데이터베이스
├── distributed-systems.md            # 분산 시스템
├── software-engineering.md           # 소프트웨어 공학과 아키텍처
├── git.md                            # Git과 버전 관리
├── web-development.md                # 웹 개발
├── cloud-devops.md                   # 클라우드 & DevOps
├── security.md                       # 보안
├── infrastructure-tools.md           # 인프라 도구
├── blockchain.md                     # 블록체인
│
├── programming/                      # 프로그래밍 언어
│   └── cpp.md                        # C++
│
└── ai/                               # AI & 머신러닝
    ├── machine-learning.md           # 머신러닝과 딥러닝
    ├── llm-agents.md                 # LLM과 AI 에이전트
    └── vision-graphics.md            # 컴퓨터 비전과 그래픽스
```

### 파일 구조 설명
- 핵심 과목들은 루트에 `.md` 파일로 배치
- `programming/`: 프로그래밍 언어별 파일 (C++, Python 등 확장 가능)
- `ai/`: AI/ML 관련 파일들을 그룹핑

---

## 4. 원본 Wiki에서 가져온 핵심 내용

### SW 아키텍처 관련
- SDLC 방법론 (Waterfall, Iterative, Agile, Unified Process)
- OOP 4대 특성 (추상화, 캡슐화, 상속, 다형성)
- SOLID 원칙
- 클래스 간 관계 결합도: Dependency < Association < Aggregation < Composition < Inheritance
- 디자인 패턴 (Factory Method, Composite, Strategy)
- 품질 속성 (Modifiability, Availability, Security, Usability, Performance)
- GREEN Methodology

### 운영체제 관련
- 프로세스 vs 스레드
- 컨텍스트 스위칭
- 스케줄링 알고리즘 (FCFS, SJF, Round Robin, SRTF)
- 페이징, TLB, 페이지 교체 알고리즘
- 동기화 (Mutex, Semaphore)
- 데드락 4가지 조건

### 네트워크 관련
- OSI 7계층 vs TCP/IP 4계층
- TCP vs UDP, 3-way handshake
- HTTP/HTTPS 차이
- Hub, Switch, Router 차이

### 클라우드 관련
- IaaS, PaaS, SaaS, FaaS
- Docker vs VM
- 컨테이너 오케스트레이션

### 소프트웨어 공학 관련
- ISTQB 테스팅 7원칙
- Verification vs Validation

### 기본 개념
- Interpreter vs Compiler: 인터프리터는 "한 줄씩 읽어 들여 해석하고 그때마다 코드를 직접 실행", 컴파일러는 "전체 코드를 기계어로 변환하는 과정을 거쳐 실행 파일을 생성"
- Shallow Copy vs Deep Copy: 얕은 복사는 참조 공유, 깊은 복사는 "완전히 별개의 메모리 주소를 가지며 객체 내부의 모든 객체들까지 재귀적으로 복사"

---

## 5. 작업 시 주의사항

1. **기존 내용 누락 금지**: Wiki에 있던 의미 있는 내용은 모두 포함
2. **빈 내용 정리**: 작성되지 않은 부분은 삭제하거나 "작성 필요"로 표시
3. **일관성 유지**: 모든 챕터가 동일한 스타일로 작성되어야 함
4. **면접 관점**: 실제 면접에서 물어볼 만한 내용 중심으로 구성

---

## 6. 변경 이력

### 2025년 1월 3차 개정
- **신규 추가**: Git과 버전 관리 (git.md) - Git 기본, 브랜치, Merge/Rebase, 워크플로우, 고급 기능
- **신규 추가**: Linux/Unix 기초 (linux.md) - 파일 시스템, 명령어, 권한, 프로세스, 셸 스크립팅
- C++ 챕터 동시성 프로그래밍 섹션 추가 (std::thread, mutex, atomic, async/future)
- CS 기초 챕터 컴파일러 이론 섹션 추가 (컴파일 과정, 어휘/구문/의미 분석, 최적화)
- 운영체제 챕터 프로세스 메모리 구조 섹션 추가 (코드/데이터/힙/스택 영역)

### 2025년 1월 2차 개정
- 컴퓨터 구조 챕터 하드웨어 지식 보강 (GPU, ISA, 메모리 종류, 인터럽트, DMA)
- **신규 추가**: 00. 컴퓨터 공학 기초 (프로그래밍 역사, 펌웨어/미들웨어, SDK/프레임워크)
- **신규 추가**: 14. 인프라 도구 (Redis, Kafka, Elasticsearch, RabbitMQ)
- **신규 추가**: 15. 컴퓨터 비전과 그래픽스 (CNN, 객체 탐지, 렌더링 파이프라인)
- **신규 추가**: 16. 블록체인 (합의 알고리즘, 스마트 컨트랙트, DeFi)
- 전체 목차를 17개 챕터, 11개 Part로 재구성

### 2025년 1월 1차 개정
- 자료구조 + 알고리즘 → 하나의 챕터로 통합
- 소프트웨어 공학 + 소프트웨어 아키텍처 → 하나의 챕터로 통합
- **신규 추가**: 12. 머신러닝과 딥러닝
- **신규 추가**: 13. LLM과 AI 에이전트
- 전체 목차를 13개 챕터, 8개 Part로 재구성

