# 11. 클라우드 & DevOps

---

## 1. 클라우드 서비스

### 클라우드란?

온라인 사업자의 리소스를 대여하여 사용하는 방식

**장점:**
- 서버 비용 최소화
- 상황별 리소스 조절 가능 (탄력성)
- 글로벌 배포 용이

**단점:**
- 계정 노출 시 전체 인프라 위험
- 벤더 종속성

### 서비스 유형

| 유형 | 설명 | 예시 |
|------|------|------|
| **IaaS** | 하드웨어/저장공간 대여 | AWS EC2, GCP Compute Engine |
| **PaaS** | 플랫폼 환경 제공 (CI/CD 포함) | Heroku, AWS Elastic Beanstalk |
| **SaaS** | 소프트웨어 온라인 이용 | Gmail, Slack, Notion |
| **FaaS** | 서버리스 아키텍처 | AWS Lambda, GCP Functions |

### 지탱 기술

| 기술 | 설명 |
|------|------|
| **리눅스 컨테이너** | OS 부팅 없이 커널 공유 방식 |
| **Master/Replica DB** | 읽기 전용 복제본 동기화 |
| **오브젝트 스토리지** | 메타데이터와 함께 저장 (S3) |
| **VLAN/VPN** | 네트워크 가상화 기술 |

---

## 2. 컨테이너

### ⭕ Docker를 사용하는 이유는?

개발 환경과 배포 환경의 버전 차이로 인한 문제를 해결한다.

- 독립적인 환경 구성
- 이식성 (어디서든 동일하게 실행)
- 빠른 시작 시간
- 자원 효율성

### ⭕ Docker와 VM의 차이는?

| 구분 | Docker | Virtual Machine |
|------|--------|-----------------|
| 가상화 수준 | OS 레벨 (컨테이너) | 하드웨어 레벨 (하이퍼바이저) |
| 커널 | 호스트 OS 공유 | 각자 독립 OS |
| 시작 시간 | 초 단위 | 분 단위 |
| 자원 사용 | 가벼움 | 무거움 |
| 격리 수준 | 낮음 | 높음 |
| 다른 OS 지원 | 제한적 | 가능 |

### Docker 핵심 개념

| 개념 | 설명 |
|------|------|
| **Image** | 컨테이너 실행에 필요한 모든 것을 포함한 템플릿 |
| **Container** | 이미지의 실행 인스턴스 |
| **Dockerfile** | 이미지 빌드 스크립트 |
| **Registry** | 이미지 저장소 (Docker Hub) |

### Dockerfile 예시

```dockerfile
FROM node:18
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "server.js"]
```

---

## 3. 오케스트레이션

### 컨테이너 오케스트레이션

다수의 컨테이너를 자동으로 배포, 관리, 확장

**주요 도구:**
- Docker Swarm
- Apache Mesos
- **Kubernetes** (사실상 표준)

### Kubernetes 핵심 개념

| 개념 | 설명 |
|------|------|
| Pod | 최소 배포 단위, 하나 이상의 컨테이너 |
| Service | Pod에 대한 네트워크 접근점 |
| Deployment | Pod의 선언적 업데이트 |
| Ingress | 외부 HTTP 트래픽 라우팅 |

---

## 4. CI/CD

### 파이프라인

```
코드 커밋 → 빌드 → 테스트 → 스테이징 → 프로덕션
    │         │        │         │          │
   Git     Docker   Unit/E2E   검증환경    배포
```

### CI/CD 도구

| 도구 | 특징 |
|------|------|
| Jenkins | 오픈소스, 플러그인 풍부 |
| GitHub Actions | GitHub 통합, YAML 설정 |
| GitLab CI | GitLab 통합 |
| CircleCI | 클라우드 기반 |

---

## 5. 인프라 관리

### IaC (Infrastructure as Code)

인프라를 코드로 관리

| 도구 | 특징 |
|------|------|
| Terraform | 멀티 클라우드 지원 |
| AWS CloudFormation | AWS 전용 |
| Ansible | 구성 관리, 에이전트리스 |

### ⭕ 배포 전략 ◑

| 전략 | 설명 | 장점 | 단점 |
|------|------|------|------|
| Rolling | 점진적으로 인스턴스 교체 | 무중단 배포 | 롤백 느림 |
| Blue-Green | 두 환경 전환 | 빠른 롤백 | 리소스 2배 필요 |
| Canary | 일부 트래픽에 먼저 배포 | 위험 최소화 | 구현 복잡 |

---

## 6. 모니터링과 로깅 ◑

### 모니터링 도구 ◑

| 도구 | 용도 |
|------|------|
| Prometheus | 메트릭 수집 (Pull 기반) |
| Grafana | 시각화 대시보드 |
| ELK Stack | 로그 수집/분석 (Elasticsearch, Logstash, Kibana) |
| Jaeger / Zipkin | 분산 트레이싱 |

### 핵심 메트릭 ◑

| 구분 | 메트릭 예시 |
|------|------------|
| RED | Rate, Errors, Duration (요청 중심) |
| USE | Utilization, Saturation, Errors (리소스 중심) |

---

## 면접 대비 체크리스트 ◑

- [ ] IaaS, PaaS, SaaS, FaaS 차이
- [ ] Docker vs VM
- [ ] Docker Image, Container, Dockerfile 개념
- [ ] Kubernetes 핵심 개념 (Pod, Service, Deployment)
- [ ] CI/CD 파이프라인
- [ ] IaC 개념과 도구 (Terraform)
- [ ] 배포 전략 (Rolling, Blue-Green, Canary)
