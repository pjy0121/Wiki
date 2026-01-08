# 13. LLM과 AI 에이전트 (LLM & AI Agents) ◑

---

## 1. Transformer 아키텍처 ◑

### ⭕ Transformer란?

2017년 "Attention Is All You Need" 논문에서 제안된 아키텍처로, RNN 없이 Attention만으로 순차 데이터를 처리한다.

### 핵심 구성 요소 ◑

| 구성 요소 | 설명 |
|-----------|------|
| Self-Attention | 시퀀스 내 모든 토큰 간 관계 학습 |
| Multi-Head Attention | 여러 관점에서 Attention 수행 |
| Positional Encoding | 순서 정보 주입 |
| Feed-Forward Network | 각 위치별 비선형 변환 |
| Layer Normalization | 학습 안정화 |

### ⭕ Self-Attention이란?

시퀀스 내 각 토큰이 다른 모든 토큰과의 관계를 학습하는 메커니즘이다.

```
Attention(Q, K, V) = softmax(QKᵀ / √d_k) V
```

| 요소 | 역할 |
|------|------|
| Query (Q) | 현재 토큰이 무엇을 찾는지 |
| Key (K) | 각 토큰이 무엇을 제공하는지 |
| Value (V) | 실제 정보 |

### Transformer 아키텍처 유형 ◑

| 유형 | 구조 | 대표 모델 |
|------|------|----------|
| Encoder-only | 양방향 문맥 이해 | BERT |
| Decoder-only | 자기회귀 생성 | GPT, Claude, LLaMA |
| Encoder-Decoder | 입력→출력 변환 | T5, BART |

---

## 2. 대규모 언어 모델 (LLM) ◑

### ⭕ LLM이란?

수십억~수조 개의 파라미터를 가진 대규모 언어 모델로, 방대한 텍스트 데이터로 사전 학습되어 다양한 언어 작업을 수행한다.

### 주요 모델 ◑

| 모델 | 개발사 | 특징 |
|------|--------|------|
| GPT-4 | OpenAI | 멀티모달, 추론 능력 |
| Claude | Anthropic | 안전성, 긴 컨텍스트 |
| LLaMA | Meta | 오픈소스 |
| Gemini | Google | 멀티모달, 긴 컨텍스트 |
| Mistral | Mistral AI | 효율적, 오픈소스 |

### ⭕ 토큰화(Tokenization)란?

텍스트를 모델이 처리할 수 있는 단위(토큰)로 분할하는 과정이다.

| 방식 | 설명 |
|------|------|
| BPE (Byte-Pair Encoding) | 빈도 기반 병합, GPT 사용 |
| WordPiece | BERT 사용 |
| SentencePiece | 언어 독립적 |

### 컨텍스트 윈도우 ◑

모델이 한 번에 처리할 수 있는 최대 토큰 수이다.

- GPT-4: 128K
- Claude: 200K
- Gemini: 1M+

---

## 3. 프롬프트 엔지니어링 ◑

### ⭕ 프롬프트 엔지니어링이란?

LLM에서 원하는 출력을 얻기 위해 입력 프롬프트를 설계하는 기법이다.

### 주요 기법 ◑

| 기법 | 설명 |
|------|------|
| Zero-shot | 예시 없이 직접 질문 |
| Few-shot | 몇 가지 예시 제공 후 질문 |
| Chain-of-Thought (CoT) | 단계별 추론 유도 |
| Self-Consistency | 여러 추론 경로 후 다수결 |
| ReAct | 추론과 행동 교차 수행 |

### Chain-of-Thought 예시 ◑

```
Q: Roger has 5 tennis balls. He buys 2 cans. Each can has 3 balls. How many now?

# 일반 프롬프트
A: 7 (잘못됨 - 5 + 2로 단순 계산)

# CoT 프롬프트 ("Let's think step by step" 추가)
A: Roger는 5개로 시작
   2캔 × 3개 = 6개 구매
   5 + 6 = 11개
   답: 11
```

복잡한 추론 문제에서 중간 단계를 명시하면 정확도가 향상된다.

---

## 4. RAG (Retrieval-Augmented Generation) ◑

### ⭕ RAG란?

외부 지식을 검색하여 LLM의 응답에 통합하는 기법이다.

### RAG 아키텍처 ◑

```
질문 → 임베딩 → 벡터 DB 검색 → 관련 문서 추출 → LLM에 컨텍스트로 제공 → 응답 생성
```

| 구성 요소 | 역할 |
|-----------|------|
| Embedding Model | 텍스트를 벡터로 변환 |
| Vector Database | 벡터 저장 및 유사도 검색 |
| LLM | 검색된 정보로 응답 생성 |

### ⭕ RAG를 사용하는 이유는?

| 장점 | 설명 |
|------|------|
| 최신 정보 제공 | 학습 데이터 이후 정보 접근 |
| 환각 감소 | 실제 문서 기반 응답 |
| 도메인 특화 | 특정 분야 지식 주입 |
| 비용 효율 | 파인튜닝 없이 지식 추가 |

### 벡터 데이터베이스 ◑

| DB | 특징 |
|-----|------|
| Pinecone | 관리형, 확장성 |
| Weaviate | 오픈소스, 하이브리드 검색 |
| ChromaDB | 경량, 로컬 개발 |
| FAISS | Facebook, 고성능 |
| Milvus | 대규모 분산 |

---

## 5. AI 에이전트 ◑

### ⭕ AI 에이전트란?

자율적으로 목표를 설정하고, 계획을 수립하며, 도구를 사용하여 작업을 수행하는 AI 시스템이다.

### 에이전트 구성 요소 ◑

| 구성 요소 | 역할 |
|-----------|------|
| **LLM (두뇌)** | 추론, 계획, 의사결정 |
| **메모리** | 과거 상호작용 기억 |
| **도구 (Tools)** | API, 검색, 코드 실행 등 |
| **계획 모듈** | 목표를 하위 작업으로 분해 |

### ⭕ 에이전트와 일반 LLM 애플리케이션의 차이는?

| 구분 | 일반 LLM | 에이전트 |
|------|----------|----------|
| 상호작용 | 단일 프롬프트-응답 | 다중 턴, 상태 유지 |
| 계획 | 없음 | 다단계 계획 수립 |
| 도구 사용 | 제한적 | API, DB 등 활용 |
| 자율성 | 낮음 | 높음 |

### 에이전트 프레임워크 ◑

| 프레임워크 | 특징 |
|------------|------|
| LangChain | 체인/에이전트 구성, 가장 인기 |
| LangGraph | 상태 기반 워크플로우 |
| AutoGen (MS) | 다중 에이전트 협업 |
| CrewAI | 역할 기반 팀 에이전트 |
| Haystack | 검색 중심 파이프라인 |

### Agentic 디자인 패턴 ◑

| 패턴 | 설명 |
|------|------|
| ReAct | 추론(Reasoning)과 행동(Action) 교차 |
| Reflection | 자기 평가 및 수정 |
| Planning | 목표를 하위 작업으로 분해 |
| Tool Use | 외부 도구/API 호출 |
| Multi-Agent | 여러 에이전트 협업 |

---

## 6. 파인튜닝 ◑

### ⭕ 파인튜닝이란?

사전 학습된 모델을 특정 작업/도메인에 맞게 추가 학습하는 것이다.

### 파인튜닝 방법 ◑

| 방법 | 설명 |
|------|------|
| Full Fine-tuning | 모든 파라미터 업데이트 |
| LoRA | 저랭크 어댑터만 학습 |
| QLoRA | 양자화 + LoRA |
| Prefix Tuning | 프롬프트 벡터만 학습 |

### ⭕ RAG vs 파인튜닝, 언제 사용하는가?

| 상황 | 적합한 방법 |
|------|-------------|
| 최신/동적 정보 필요 | RAG |
| 특정 도메인 스타일/지식 | 파인튜닝 |
| 빠른 프로토타입 | RAG |
| 비용/리소스 제한 | RAG |

---

## 7. LLM 평가 ◑

### 평가 지표 ◑

| 지표 | 설명 |
|------|------|
| Perplexity | 언어 모델의 예측 불확실성 |
| BLEU | 기계 번역 품질 |
| ROUGE | 요약 품질 |
| Human Evaluation | 인간 평가 |
| Benchmark (MMLU, HumanEval) | 표준화된 테스트 |

### 환각 (Hallucination) ◑

모델이 사실이 아닌 정보를 생성하는 현상이다.

**완화 방법:**
- RAG 사용
- 낮은 Temperature
- 출처 요청
- Fact-checking 도구

---

## 8. 보안 및 안전 ◑

### 프롬프트 인젝션 ◑

악의적인 입력으로 모델의 동작을 조작하는 공격이다.

| 유형 | 설명 |
|------|------|
| 직접 인젝션 | 사용자가 직접 악성 프롬프트 입력 |
| 간접 인젝션 | 외부 데이터(웹페이지 등)에 악성 명령 삽입 |

### 안전 고려사항 ◑

- 입력 검증 및 필터링
- 권한 최소화
- 샌드박싱
- 출력 모니터링
- 레이트 리미팅

---

## 면접 대비 체크리스트 ◑

- [ ] Transformer와 Self-Attention 원리
- [ ] Encoder-only vs Decoder-only 차이
- [ ] 프롬프트 엔지니어링 기법 (Zero-shot, Few-shot, CoT)
- [ ] RAG 아키텍처와 사용 이유
- [ ] AI 에이전트의 구성 요소
- [ ] 에이전트 vs 일반 LLM 애플리케이션 차이
- [ ] 파인튜닝 vs RAG 선택 기준
- [ ] LLM 환각과 완화 방법
- [ ] 프롬프트 인젝션 공격

