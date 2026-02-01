# 11. 클라우드 & DevOps

---

## 1. 클라우드 서비스

### 클라우드란?

**클라우드 컴퓨팅**은 인터넷을 통해 서버, 스토리지, 네트워크 등의 컴퓨팅 자원을 필요한 만큼 빌려 쓰는 방식이다. 과거에는 서비스를 운영하려면 직접 서버를 구매하고, 데이터센터에 설치하고, 관리 인력을 두어야 했다. 클라우드는 이 모든 것을 AWS, GCP, Azure 같은 클라우드 제공자에게 맡기고, 사용한 만큼만 비용을 지불하는 모델이다.

**장점:**
- **비용 효율성**: 초기 서버 구매 비용 없이 사용량 기반 과금
- **탄력성 (Elasticity)**: 트래픽이 급증하면 서버를 늘리고, 줄어들면 다시 줄일 수 있음
- **글로벌 배포**: 전 세계 데이터센터에 몇 번의 클릭으로 서비스 배포 가능
- **관리 부담 감소**: 하드웨어 유지보수, 보안 패치 등을 제공자가 담당

**단점:**
- **보안 리스크**: 클라우드 계정이 해킹되면 전체 인프라가 노출됨
- **벤더 종속성 (Vendor Lock-in)**: 특정 클라우드의 고유 서비스를 많이 쓸수록 이전이 어려워짐
- **네트워크 의존성**: 인터넷 연결이 끊기면 서비스 접근 불가

### ⭕ 서비스 유형 (IaaS, PaaS, SaaS, FaaS)

클라우드 서비스는 **제공자가 얼마나 많은 부분을 관리해주는가**에 따라 분류된다. 피자에 비유하면 이해하기 쉽다.

```
┌─────────────────────────────────────────────────────────────────────┐
│                    클라우드 서비스 유형 비교                          │
├─────────────────────────────────────────────────────────────────────┤
│   On-Premise    │    IaaS      │    PaaS      │    SaaS/FaaS       │
│   (직접 관리)    │              │              │                    │
├─────────────────────────────────────────────────────────────────────┤
│   애플리케이션   │ 애플리케이션  │ 애플리케이션  │ ████████████████   │
│   데이터        │ 데이터       │ 데이터        │ ████████████████   │
│   런타임        │ 런타임       │ ████████████ │ ████████████████   │
│   미들웨어      │ 미들웨어     │ ████████████ │ ████████████████   │
│   OS           │ OS          │ ████████████ │ ████████████████   │
│   가상화        │ ████████████│ ████████████ │ ████████████████   │
│   서버         │ ████████████ │ ████████████ │ ████████████████   │
│   스토리지      │ ████████████ │ ████████████ │ ████████████████   │
│   네트워크      │ ████████████ │ ████████████ │ ████████████████   │
├─────────────────────────────────────────────────────────────────────┤
│ ████ = 제공자가 관리  │  빈칸 = 사용자가 관리                         │
└─────────────────────────────────────────────────────────────────────┘
```

**IaaS (Infrastructure as a Service)** ◑

가장 기본적인 형태로, **가상 서버, 스토리지, 네트워크**만 제공받는다. OS 설치부터 애플리케이션 배포까지 모두 직접 해야 한다.

- **피자 비유**: 주방과 오븐만 빌려서 직접 피자를 만드는 것
- **예시**: AWS EC2, GCP Compute Engine, Azure VM
- **사용 사례**: 기존 서버 환경을 그대로 클라우드로 옮기고 싶을 때, 세밀한 서버 제어가 필요할 때

**PaaS (Platform as a Service)** ◑

**개발에 필요한 플랫폼 전체**를 제공받는다. 서버, OS, 런타임, 데이터베이스가 이미 준비되어 있어서 코드만 올리면 바로 실행된다.

- **피자 비유**: 토핑만 올리면 되는 반죽과 소스가 준비된 피자 키트
- **예시**: Heroku, AWS Elastic Beanstalk, Google App Engine
- **사용 사례**: 인프라 관리 없이 빠르게 애플리케이션을 배포하고 싶을 때

**SaaS (Software as a Service)** ◑

**완성된 소프트웨어**를 인터넷으로 바로 사용한다. 설치도, 관리도 필요 없다.

- **피자 비유**: 배달 시켜서 바로 먹는 피자
- **예시**: Gmail, Slack, Notion, Google Docs
- **사용 사례**: 일반 사용자가 소프트웨어를 바로 사용할 때

**FaaS (Function as a Service)** ◑

**함수 단위로 코드를 실행**하는 서버리스 모델이다. 서버를 띄워놓지 않고, 요청이 올 때만 함수가 실행되고 그 시간만큼만 과금된다.

- **피자 비유**: 배가 고플 때마다 한 조각씩 주문하는 것
- **예시**: AWS Lambda, Google Cloud Functions, Azure Functions
- **사용 사례**: 이벤트 기반 처리 (이미지 업로드 시 썸네일 생성, API 요청 처리 등)

```javascript
// AWS Lambda 함수 예시
exports.handler = async (event) => {
    const name = event.name || 'World';
    return {
        statusCode: 200,
        body: `Hello, ${name}!`
    };
};
// 이 함수는 호출될 때만 실행되고, 실행 시간(ms)에 따라 과금됨
```

| 유형 | 관리 범위 | 유연성 | 관리 부담 | 예시 |
|------|----------|--------|----------|------|
| **IaaS** | 인프라만 제공 | 높음 | 높음 | AWS EC2 |
| **PaaS** | 플랫폼까지 제공 | 중간 | 낮음 | Heroku |
| **SaaS** | 소프트웨어 제공 | 낮음 | 없음 | Gmail |
| **FaaS** | 함수 실행 환경 | 중간 | 매우 낮음 | Lambda |

### 클라우드 지탱 기술 ◑

클라우드가 가능한 이유는 여러 핵심 기술들 덕분이다.

**리눅스 컨테이너**

전통적인 가상화(VM)는 각 가상 머신마다 별도의 OS를 설치해야 해서 무겁다. 컨테이너는 **호스트 OS의 커널을 공유**하면서 프로세스만 격리하기 때문에 훨씬 가볍고 빠르다. Docker가 대표적이다.

**Master/Replica 데이터베이스**

하나의 DB로는 수많은 읽기 요청을 감당하기 어렵다. **Master DB**는 쓰기를 담당하고, 여러 **Replica(복제본) DB**가 읽기를 담당하도록 분산한다. Master에 쓴 데이터는 Replica로 자동 동기화된다.

```
쓰기 요청 ──▶ [Master DB] ──동기화──▶ [Replica 1] ◀── 읽기 요청
                          └──동기화──▶ [Replica 2] ◀── 읽기 요청
                          └──동기화──▶ [Replica 3] ◀── 읽기 요청
```

**오브젝트 스토리지**

파일을 폴더 구조가 아닌 **고유한 키(Key)**로 저장하는 방식이다. AWS S3가 대표적이며, 이미지, 동영상, 백업 파일 등 대용량 비정형 데이터 저장에 적합하다. 각 파일에는 메타데이터(생성일, 크기, 사용자 정의 태그 등)를 함께 저장할 수 있다.

**VLAN/VPN**

- **VLAN (Virtual LAN)**: 물리적으로 같은 네트워크를 **논리적으로 분리**하여 보안과 성능을 높임
- **VPN (Virtual Private Network)**: 인터넷을 통해 **암호화된 사설 네트워크**를 구성하여 안전하게 통신

---

## 2. 컨테이너

### ⭕ Docker를 사용하는 이유는?

개발자 A의 노트북에서는 잘 돌아가던 코드가 서버에 배포하면 안 되는 경우가 흔하다. Python 버전이 다르거나, 특정 라이브러리가 설치되어 있지 않거나, 환경 변수가 다르기 때문이다. 이를 **"It works on my machine"** 문제라고 부른다.

**Docker**는 애플리케이션과 그 실행에 필요한 모든 것(코드, 런타임, 라이브러리, 환경 변수)을 하나의 **컨테이너**로 패키징한다. 이 컨테이너는 어디서 실행하든 동일하게 동작한다.

```
┌─────────────────────────────────────────────────────────────┐
│                     Docker 없이 배포                         │
│                                                             │
│   개발 환경              프로덕션 서버                        │
│   ┌──────────┐          ┌──────────┐                        │
│   │ Python   │          │ Python   │                        │
│   │ 3.11     │    ?     │ 3.8      │  ← 버전 불일치!         │
│   │ + 라이브러리│         │ + ???    │  ← 라이브러리 누락!      │
│   └──────────┘          └──────────┘                        │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                     Docker로 배포                           │
│                                                             │
│   개발 환경              프로덕션 서버                        │
│   ┌──────────┐          ┌──────────┐                        │
│   │ Container│    =     │ Container│  ← 완전히 동일!         │
│   │ (모든 것 │          │ (모든 것 │                         │
│   │  포함)   │          │  포함)   │                         │
│   └──────────┘          └──────────┘                        │
└─────────────────────────────────────────────────────────────┘
```

**Docker의 핵심 장점:**
- **환경 일관성**: 개발, 테스트, 프로덕션 환경이 동일
- **빠른 배포**: 이미지만 받아서 실행하면 됨
- **격리**: 애플리케이션 간 충돌 없음 (Node 16 앱과 Node 20 앱을 동시에 실행 가능)
- **버전 관리**: 이미지에 태그를 붙여 버전 관리 가능 (v1.0, v1.1 등)

### ⭕ Docker와 VM의 차이는?

둘 다 애플리케이션을 격리된 환경에서 실행한다는 점은 같지만, **가상화 방식**이 근본적으로 다르다.

**Virtual Machine (가상 머신)**은 **하드웨어를 가상화**한다. 각 VM은 자체 OS를 갖고 있어서 완전히 독립된 컴퓨터처럼 동작한다. 하지만 OS를 부팅해야 하므로 무겁고 느리다.

**Docker (컨테이너)**는 **OS 레벨에서 가상화**한다. 호스트 OS의 커널을 공유하고 프로세스만 격리하기 때문에 가볍고 빠르다.

```
         Virtual Machine                         Docker Container

┌─────────────────────────┐           ┌─────────────────────────┐
│   App A   │   App B     │           │   App A   │   App B     │
├───────────┼─────────────┤           ├───────────┴─────────────┤
│  Guest OS │  Guest OS   │           │     Docker Engine       │
├───────────┴─────────────┤           ├─────────────────────────┤
│      Hypervisor         │           │        Host OS          │
├─────────────────────────┤           ├─────────────────────────┤
│       Hardware          │           │        Hardware         │
└─────────────────────────┘           └─────────────────────────┘

 - 각 앱마다 OS 필요 (수 GB)          - 커널 공유, OS 불필요 (수 MB)
 - 부팅에 수 분 소요                   - 시작에 수 초 소요
```

| 구분 | Docker | Virtual Machine |
|------|--------|-----------------|
| 가상화 수준 | OS 레벨 (프로세스 격리) | 하드웨어 레벨 (하이퍼바이저) |
| 커널 | 호스트 OS 커널 공유 | 각 VM이 독립 OS 보유 |
| 시작 시간 | **초 단위** | 분 단위 |
| 이미지 크기 | **수십~수백 MB** | 수 GB |
| 격리 수준 | 상대적으로 낮음 | 높음 (완전한 OS 격리) |
| 다른 OS 지원 | 제한적 (Linux 컨테이너는 Linux에서만) | 가능 (Windows에서 Linux VM 실행 가능) |

> **언제 VM을 사용할까?** 다른 OS가 필요하거나(Windows에서 Linux), 완전한 격리가 필요한 보안 환경에서 사용한다. 대부분의 애플리케이션 배포에는 Docker가 더 효율적이다.

### Docker 핵심 개념 ◑

Docker를 이해하려면 **Image**, **Container**, **Dockerfile**, **Registry**의 관계를 알아야 한다.

```
┌─────────────────────────────────────────────────────────────────┐
│                         Docker 워크플로우                        │
│                                                                 │
│   Dockerfile          Image              Container              │
│   ┌─────────┐        ┌─────────┐        ┌─────────┐            │
│   │ 빌드    │ build  │ 템플릿   │  run   │ 실행    │            │
│   │ 스크립트 │ ────▶ │ (읽기전용)│ ────▶ │ 인스턴스 │            │
│   └─────────┘        └─────────┘        └─────────┘            │
│                           │                  │                  │
│                           │ push/pull        │                  │
│                           ▼                  │                  │
│                      ┌─────────┐             │                  │
│                      │Registry │ ◀───────────┘                  │
│                      │(Docker  │   이미지 공유                   │
│                      │  Hub)   │                                │
│                      └─────────┘                                │
└─────────────────────────────────────────────────────────────────┘
```

**Image (이미지)**

컨테이너를 만들기 위한 **읽기 전용 템플릿**이다. 애플리케이션 코드, 런타임, 라이브러리, 환경 변수 등 실행에 필요한 모든 것을 포함한다. 클래스와 비슷하다고 생각하면 된다.

```bash
# 이미지 목록 확인
docker images

# 이미지 다운로드
docker pull nginx:latest
```

**Container (컨테이너)**

이미지를 실행한 **인스턴스**이다. 하나의 이미지로 여러 컨테이너를 만들 수 있다. 컨테이너는 격리된 환경에서 실행되며, 종료되면 내부 데이터는 사라진다 (Volume을 사용하면 보존 가능).

```bash
# 컨테이너 실행
docker run -d -p 8080:80 nginx

# 실행 중인 컨테이너 목록
docker ps

# 컨테이너 중지
docker stop <container_id>
```

**Dockerfile**

이미지를 어떻게 만들지 정의하는 **빌드 스크립트**이다. 기반 이미지, 파일 복사, 명령 실행 등을 순서대로 작성한다.

**Registry (레지스트리)**

이미지를 저장하고 공유하는 **저장소**이다. Docker Hub가 가장 유명하며, 회사 내부용으로 프라이빗 레지스트리를 운영할 수도 있다.

### Dockerfile 예시 ◑

Dockerfile의 각 명령어가 무엇을 하는지 이해하는 것이 중요하다.

```dockerfile
# 1. 기반 이미지 지정
FROM node:18
# Node.js 18 버전이 설치된 이미지를 기반으로 시작

# 2. 작업 디렉토리 설정
WORKDIR /app
# 이후 명령들이 /app 디렉토리에서 실행됨

# 3. 의존성 파일 먼저 복사 (캐싱 최적화)
COPY package*.json ./
# package.json과 package-lock.json을 먼저 복사

# 4. 의존성 설치
RUN npm install
# 의존성만 먼저 설치해두면, 코드가 바뀌어도 이 단계는 캐시됨

# 5. 나머지 소스 코드 복사
COPY . .
# 전체 소스 코드를 컨테이너로 복사

# 6. 포트 문서화
EXPOSE 3000
# 컨테이너가 3000번 포트를 사용함을 명시 (실제 포트 열기는 run -p로)

# 7. 실행 명령
CMD ["node", "server.js"]
# 컨테이너 시작 시 실행할 명령
```

**주요 Dockerfile 명령어:**

| 명령어 | 설명 | 예시 |
|--------|------|------|
| `FROM` | 기반 이미지 지정 | `FROM python:3.11-slim` |
| `WORKDIR` | 작업 디렉토리 설정 | `WORKDIR /app` |
| `COPY` | 파일/디렉토리 복사 | `COPY . .` |
| `RUN` | 빌드 시 명령 실행 | `RUN pip install -r requirements.txt` |
| `ENV` | 환경 변수 설정 | `ENV NODE_ENV=production` |
| `EXPOSE` | 포트 문서화 | `EXPOSE 8080` |
| `CMD` | 컨테이너 시작 명령 | `CMD ["python", "app.py"]` |

**이미지 빌드 및 실행:**

```bash
# 이미지 빌드
docker build -t my-app:1.0 .

# 컨테이너 실행
docker run -d -p 3000:3000 my-app:1.0
# -d: 백그라운드 실행
# -p 3000:3000: 호스트 3000 포트를 컨테이너 3000 포트에 연결
```

---

## 3. 오케스트레이션

### 컨테이너 오케스트레이션

다수의 컨테이너를 자동으로 배포, 관리, 확장

**주요 도구:**
- Docker Swarm
- Apache Mesos
- **Kubernetes** (사실상 표준)

### Kubernetes 아키텍처 ◑

Kubernetes 클러스터는 **Control Plane**과 **Worker Node**로 구성된다.

```
┌─────────────────────────────────────────────────────────────┐
│                      Control Plane                          │
│  ┌─────────────┐  ┌──────────────┐  ┌─────────────────────┐ │
│  │ API Server  │  │  Scheduler   │  │ Controller Manager  │ │
│  └─────────────┘  └──────────────┘  └─────────────────────┘ │
│  ┌─────────────────────────────────────────────────────────┐│
│  │                        etcd                              ││
│  └─────────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────┘
         │                    │                    │
    ┌────▼────┐          ┌────▼────┐          ┌────▼────┐
    │ Node 1  │          │ Node 2  │          │ Node 3  │
    │┌───────┐│          │┌───────┐│          │┌───────┐│
    ││kubelet││          ││kubelet││          ││kubelet││
    │├───────┤│          │├───────┤│          │├───────┤│
    ││kube-  ││          ││kube-  ││          ││kube-  ││
    ││proxy  ││          ││proxy  ││          ││proxy  ││
    │├───────┤│          │├───────┤│          │├───────┤│
    ││ Pods  ││          ││ Pods  ││          ││ Pods  ││
    │└───────┘│          │└───────┘│          │└───────┘│
    └─────────┘          └─────────┘          └─────────┘
```

| 컴포넌트 | 역할 |
|---------|------|
| **API Server** | 모든 요청의 진입점, kubectl 명령 처리 |
| **etcd** | 클러스터 상태 저장소 (key-value) |
| **Scheduler** | Pod를 적절한 Node에 배치 |
| **Controller Manager** | 상태 관리 (Deployment, ReplicaSet 등) |
| **kubelet** | Node에서 Pod 실행 관리 |
| **kube-proxy** | 네트워크 규칙 및 로드밸런싱 |

### Kubernetes 핵심 개념

| 개념 | 설명 |
|------|------|
| Pod | 최소 배포 단위, 하나 이상의 컨테이너 |
| Service | Pod에 대한 네트워크 접근점 |
| Deployment | Pod의 선언적 업데이트 |
| Ingress | 외부 HTTP 트래픽 라우팅 |

### Pod ◑

**가장 작은 배포 단위**로, 하나 이상의 컨테이너를 포함한다. 같은 Pod 내 컨테이너는 네트워크(localhost)와 스토리지를 공유한다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app
spec:
  containers:
  - name: app
    image: nginx:1.21
    ports:
    - containerPort: 80
```

**특징:**
- 일반적으로 하나의 컨테이너만 포함 (1 Pod = 1 Container)
- Sidecar 패턴: 메인 컨테이너 + 보조 컨테이너 (로깅, 프록시 등)
- Pod는 일시적(ephemeral)이므로 직접 생성하지 않고 Deployment로 관리

### ReplicaSet과 Deployment ◑

**ReplicaSet**은 지정된 수의 Pod 복제본을 유지한다. 하지만 직접 사용하지 않고 **Deployment**를 통해 관리한다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3                    # Pod 3개 유지
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: app
        image: nginx:1.21
        ports:
        - containerPort: 80
```

**Deployment의 기능:**
- 롤링 업데이트 / 롤백
- 스케일링 (replicas 조정)
- 자동 복구 (Pod 장애 시 재생성)

### Service ◑

Pod는 동적으로 생성/삭제되므로 IP가 변한다. **Service**는 고정된 접근점(ClusterIP, DNS)을 제공한다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app              # 이 라벨을 가진 Pod로 트래픽 전달
  ports:
  - port: 80                 # Service 포트
    targetPort: 80           # Pod 포트
  type: ClusterIP            # 기본값, 클러스터 내부에서만 접근 가능
```

**Service 유형:**

| 유형 | 설명 |
|------|------|
| **ClusterIP** | 클러스터 내부 전용 (기본값) |
| **NodePort** | 각 노드의 고정 포트로 외부 노출 (30000-32767) |
| **LoadBalancer** | 클라우드 제공자의 로드밸런서 사용 |
| **ExternalName** | 외부 DNS 이름으로 매핑 |

### ⭕ Ingress란? ◑

**Ingress**는 클러스터 외부에서 내부 Service로의 HTTP/HTTPS 트래픽을 관리하는 객체이다.

**왜 필요한가?**
- NodePort: 포트 번호가 30000번대로 제한적
- LoadBalancer: Service마다 별도의 로드밸런서 필요 (비용 증가)
- **Ingress**: 하나의 진입점에서 URL 경로/도메인 기반 라우팅

```
                    ┌─────────────────────────────────────────┐
                    │              Ingress                     │
   인터넷 ──────────▶│  api.example.com  → API Service         │
                    │  web.example.com  → Web Service         │
                    │  example.com/app  → App Service         │
                    └─────────────────────────────────────────┘
```

**Ingress 예시 - 호스트 기반 라우팅:**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: multi-host-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
  # api.example.com 요청 → api-service로 라우팅
  - host: api.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 80

  # web.example.com 요청 → web-service로 라우팅
  - host: web.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
```

**Ingress 예시 - 경로 기반 라우팅:**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: path-based-ingress
spec:
  ingressClassName: nginx
  rules:
  - host: example.com
    http:
      paths:
      # example.com/api/* → api-service
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 80
      # example.com/web/* → web-service
      - path: /web
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
      # 기본 경로 → frontend-service
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend-service
            port:
              number: 80
```

**Ingress Controller:**

Ingress 리소스는 규칙만 정의하고, 실제 동작은 **Ingress Controller**가 담당한다.

| Controller | 특징 |
|-----------|------|
| **NGINX Ingress** | 가장 널리 사용, 높은 안정성 |
| **Traefik** | 자동 설정, 동적 라우팅 |
| **AWS ALB** | AWS 환경에 최적화 |
| **Istio Gateway** | 서비스 메시와 통합 |

### ConfigMap과 Secret ◑

환경 설정과 민감 정보를 Pod 외부에서 관리한다.

```yaml
# ConfigMap - 일반 설정
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  DATABASE_HOST: "db.example.com"
  LOG_LEVEL: "info"

---
# Secret - 민감 정보 (base64 인코딩)
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
type: Opaque
data:
  DB_PASSWORD: cGFzc3dvcmQxMjM=  # base64 encoded
```

**Pod에서 사용:**
```yaml
spec:
  containers:
  - name: app
    env:
    - name: DATABASE_HOST
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: DATABASE_HOST
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: app-secret
          key: DB_PASSWORD
```

---

## 4. CI/CD

### ⭕ CI/CD란? ◑

**CI/CD**는 코드 변경부터 프로덕션 배포까지의 과정을 **자동화**하는 방식이다.

과거에는 개발자가 코드를 작성하면, 별도의 빌드 담당자가 수동으로 빌드하고, QA팀이 테스트하고, 운영팀이 서버에 배포했다. 이 과정은 느리고, 실수가 발생하기 쉬우며, 배포 주기가 길어질 수밖에 없었다.

CI/CD는 이 모든 과정을 자동화하여, 개발자가 코드를 푸시하면 **몇 분 내에 자동으로 테스트되고 배포**되도록 만든다.

**CI (Continuous Integration, 지속적 통합)**

개발자들이 작성한 코드를 **자주, 자동으로 통합하고 검증**하는 방식이다.

- 개발자가 코드를 푸시할 때마다 자동으로 빌드
- 유닛 테스트, 린트 검사 자동 실행
- 문제가 있으면 즉시 알림
- **목표**: 통합 문제를 빠르게 발견하고 수정

**CD (Continuous Delivery/Deployment, 지속적 전달/배포)**

검증된 코드를 **자동으로 프로덕션에 배포**하는 방식이다.

- **Continuous Delivery**: 배포 준비까지 자동화, 실제 배포는 수동 승인
- **Continuous Deployment**: 모든 과정이 완전 자동화, 코드 푸시하면 바로 프로덕션 배포

```
┌─────────────────────────────────────────────────────────────────────┐
│                          CI/CD 파이프라인                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│   ┌────────┐    ┌────────┐    ┌────────┐    ┌────────┐    ┌──────┐ │
│   │  코드   │    │  빌드   │    │ 테스트  │    │스테이징 │    │프로덕션│ │
│   │  푸시   │───▶│        │───▶│        │───▶│  배포   │───▶│ 배포  │ │
│   └────────┘    └────────┘    └────────┘    └────────┘    └──────┘ │
│       │             │             │             │             │     │
│       │             │             │             │             │     │
│      Git         Docker        Unit Test    검증환경에서    최종     │
│      push        이미지         E2E Test    동작 확인     서비스    │
│                   빌드         Lint 검사                           │
│                                                                     │
│   ├──────────── CI ────────────┤├────────── CD ──────────────────┤ │
└─────────────────────────────────────────────────────────────────────┘
```

### 파이프라인의 각 단계 ◑

**1. 코드 푸시 (Trigger)**
- 개발자가 Git에 코드를 푸시하면 파이프라인이 시작됨
- 특정 브랜치(main, develop)나 태그에만 반응하도록 설정 가능

**2. 빌드 (Build)**
- 소스 코드를 실행 가능한 형태로 변환
- Docker 이미지 빌드, 컴파일, 의존성 설치 등
- 빌드 실패 시 파이프라인 중단 및 알림

**3. 테스트 (Test)**
- **유닛 테스트**: 개별 함수/모듈 테스트
- **통합 테스트**: 모듈 간 상호작용 테스트
- **E2E 테스트**: 사용자 시나리오 전체 테스트
- 테스트 커버리지 리포트 생성

**4. 스테이징 배포 (Staging)**
- 프로덕션과 동일한 환경에 먼저 배포
- QA 팀이 수동 테스트 진행
- 문제 발생 시 프로덕션 배포 전 차단

**5. 프로덕션 배포 (Production)**
- 실제 사용자에게 서비스 제공
- 롤링 업데이트, 블루-그린 배포 등 전략 적용
- 문제 발생 시 자동 롤백

### CI/CD 도구 ◑

**Jenkins**

가장 오래되고 널리 사용되는 오픈소스 CI/CD 도구이다. 플러그인이 매우 풍부하지만, 직접 서버를 운영해야 하고 설정이 복잡하다.

**GitHub Actions**

GitHub에 내장된 CI/CD 도구로, 레포지토리에 YAML 파일만 추가하면 바로 사용할 수 있다. GitHub 생태계와 완벽하게 통합되어 있어서 GitHub을 사용한다면 가장 편리하다.

```yaml
# .github/workflows/ci.yml
name: CI Pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build-and-test:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v4

    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '20'

    - name: Install dependencies
      run: npm ci

    - name: Run tests
      run: npm test

    - name: Build
      run: npm run build
```

| 도구 | 특징 | 적합한 경우 |
|------|------|------------|
| **Jenkins** | 오픈소스, 플러그인 풍부, 셀프호스팅 | 복잡한 파이프라인, 완전한 커스터마이징 필요 |
| **GitHub Actions** | GitHub 통합, YAML 설정, 무료 티어 | GitHub 사용, 간단한 설정 원함 |
| **GitLab CI** | GitLab 통합, 내장 컨테이너 레지스트리 | GitLab 사용 |
| **CircleCI** | 클라우드 기반, 빠른 빌드 | SaaS 형태 선호, Docker 친화적 |

---

## 5. 인프라 관리

### ⭕ IaC (Infrastructure as Code)란? ◑

**인프라를 코드로 정의하고 관리**하는 방식이다. 수동 설정 대신 코드로 인프라를 버전 관리하고 자동화한다.

**왜 필요한가?**
- 수동 설정의 문제: 휴먼 에러, 환경 불일치, 재현 불가능
- 코드화의 장점: 버전 관리, 코드 리뷰, 자동화, 재현 가능

**IaC의 두 가지 접근법:**

| 접근법 | 설명 | 도구 |
|--------|------|------|
| **선언적 (Declarative)** | "무엇을" 원하는지 정의, 도구가 현재→목표 상태로 변환 | Terraform, CloudFormation |
| **명령적 (Imperative)** | "어떻게" 할지 단계별 명령 정의 | Ansible, Shell Script |

**주요 IaC 도구 비교:**

| 도구 | 유형 | 특징 | 사용 사례 |
|------|------|------|----------|
| **Terraform** | 프로비저닝 | 멀티 클라우드, 선언적 | 인프라 생성/관리 |
| **Ansible** | 구성 관리 | 에이전트리스, 명령적 | 서버 설정/배포 |
| **CloudFormation** | 프로비저닝 | AWS 전용, 선언적 | AWS 인프라 |
| **Pulumi** | 프로비저닝 | 프로그래밍 언어 사용 | 개발자 친화적 IaC |

### Terraform ◑

HashiCorp에서 만든 **인프라 프로비저닝 도구**로, 클라우드 리소스를 선언적으로 정의한다.

**핵심 개념:**

| 개념 | 설명 |
|------|------|
| **Provider** | 클라우드/서비스 연결 (AWS, GCP, Azure 등) |
| **Resource** | 생성할 인프라 리소스 정의 |
| **State** | 현재 인프라 상태 저장 (.tfstate 파일) |
| **Module** | 재사용 가능한 리소스 묶음 |

**Terraform 워크플로우:**

```
terraform init    →  terraform plan   →  terraform apply  →  terraform destroy
(초기화/Provider     (변경사항 미리보기)    (실제 적용)         (리소스 삭제)
 다운로드)
```

**Terraform 예시 - AWS EC2 인스턴스:**

```hcl
# provider 설정
provider "aws" {
  region = "ap-northeast-2"
}

# EC2 인스턴스 생성
resource "aws_instance" "web_server" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"

  tags = {
    Name = "WebServer"
    Environment = "Production"
  }
}

# 보안 그룹
resource "aws_security_group" "web_sg" {
  name = "web-security-group"

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

# 출력값
output "instance_ip" {
  value = aws_instance.web_server.public_ip
}
```

**State 관리:**
- `.tfstate` 파일에 현재 인프라 상태 저장
- 팀 협업 시 **Remote State** 사용 (S3 + DynamoDB 락)
- State가 실제 인프라와 불일치하면 문제 발생

```hcl
# Remote State 설정
terraform {
  backend "s3" {
    bucket         = "my-terraform-state"
    key            = "prod/terraform.tfstate"
    region         = "ap-northeast-2"
    dynamodb_table = "terraform-locks"
    encrypt        = true
  }
}
```

### ⭕ Ansible이란? ◑

**에이전트리스 구성 관리 도구**로, SSH를 통해 서버를 설정하고 애플리케이션을 배포한다.

**Terraform vs Ansible:**

| 구분 | Terraform | Ansible |
|------|-----------|---------|
| 목적 | 인프라 **생성** (Provisioning) | 서버 **설정** (Configuration) |
| 접근법 | 선언적 | 명령적 (절차적) |
| 에이전트 | Provider 필요 | 에이전트리스 (SSH) |
| 상태 관리 | State 파일 | 없음 (멱등성으로 해결) |
| 사용 시점 | 서버/네트워크 생성 | 패키지 설치, 설정 파일 배포 |

> **일반적인 조합**: Terraform으로 인프라 생성 → Ansible로 서버 구성

**Ansible 핵심 개념:**

| 개념 | 설명 |
|------|------|
| **Inventory** | 관리할 서버 목록 |
| **Playbook** | 실행할 작업 정의 (YAML) |
| **Task** | 개별 작업 단위 |
| **Module** | 작업 수행 함수 (apt, copy, service 등) |
| **Role** | 재사용 가능한 Playbook 묶음 |

**Inventory 예시:**

```ini
# inventory.ini
[webservers]
web1.example.com
web2.example.com

[dbservers]
db1.example.com ansible_user=admin

[all:vars]
ansible_python_interpreter=/usr/bin/python3
```

**Playbook 예시 - Nginx 설치 및 설정:**

```yaml
# deploy_nginx.yml
---
- name: Nginx 웹서버 설정
  hosts: webservers
  become: yes  # sudo 권한으로 실행

  vars:
    nginx_port: 80
    app_name: "my-web-app"

  tasks:
    - name: Nginx 설치
      apt:
        name: nginx
        state: present
        update_cache: yes

    - name: Nginx 설정 파일 배포
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/sites-available/default
      notify: Restart Nginx

    - name: 애플리케이션 파일 복사
      copy:
        src: ./app/
        dest: /var/www/html/
        owner: www-data
        mode: '0644'

    - name: Nginx 서비스 시작
      service:
        name: nginx
        state: started
        enabled: yes

  handlers:
    - name: Restart Nginx
      service:
        name: nginx
        state: restarted
```

**실행 명령:**

```bash
# Playbook 실행
ansible-playbook -i inventory.ini deploy_nginx.yml

# 특정 호스트만 실행
ansible-playbook -i inventory.ini deploy_nginx.yml --limit web1.example.com

# Dry-run (변경 없이 확인만)
ansible-playbook -i inventory.ini deploy_nginx.yml --check
```

**멱등성 (Idempotency):**

Ansible은 **멱등성**을 보장한다. 같은 Playbook을 여러 번 실행해도 결과가 동일하다.

```yaml
# 이미 nginx가 설치되어 있으면 "ok", 없으면 "changed"
- name: Nginx 설치
  apt:
    name: nginx
    state: present  # 존재하면 설치 안 함
```

**Role 구조:**

복잡한 설정은 Role로 모듈화한다.

```
roles/
└── nginx/
    ├── tasks/
    │   └── main.yml      # 실행할 태스크
    ├── handlers/
    │   └── main.yml      # 핸들러 (서비스 재시작 등)
    ├── templates/
    │   └── nginx.conf.j2 # Jinja2 템플릿
    ├── files/
    │   └── index.html    # 정적 파일
    ├── vars/
    │   └── main.yml      # 변수
    └── defaults/
        └── main.yml      # 기본값
```

```yaml
# Role 사용
- hosts: webservers
  roles:
    - nginx
    - { role: app, app_port: 3000 }
```

### ⭕ 배포 전략 ◑

새 버전을 프로덕션에 배포할 때, **어떻게 기존 버전을 새 버전으로 교체할 것인가**가 배포 전략이다. 잘못된 배포는 서비스 장애로 이어지므로, 상황에 맞는 전략을 선택해야 한다.

**Rolling 배포 (롤링 업데이트)**

가장 일반적인 방식으로, 인스턴스를 **하나씩 순차적으로 교체**한다. 항상 일부 인스턴스가 실행 중이므로 무중단 배포가 가능하다.

```
시작 상태:     [v1] [v1] [v1] [v1]     ← 모두 구버전

1단계:        [v2] [v1] [v1] [v1]     ← 하나를 새 버전으로 교체
2단계:        [v2] [v2] [v1] [v1]     ← 다음 하나 교체
3단계:        [v2] [v2] [v2] [v1]     ← 계속 진행
완료:         [v2] [v2] [v2] [v2]     ← 모두 새 버전
```

- **장점**: 추가 리소스 불필요, 점진적 배포로 문제 감지 가능
- **단점**: 롤백 시 다시 하나씩 되돌려야 함, 배포 중 두 버전이 공존

**Blue-Green 배포**

**두 개의 동일한 환경**(Blue, Green)을 운영하고, 트래픽을 한 번에 전환한다. 현재 Blue가 운영 중이라면, Green에 새 버전을 배포한 후 로드밸런서를 Green으로 전환한다.

```
                    ┌─────────────┐
                    │ Load        │
                    │ Balancer    │
                    └──────┬──────┘
                           │
              ┌────────────┴────────────┐
              ▼                         ▼
       ┌─────────────┐           ┌─────────────┐
       │   Blue      │           │   Green     │
       │   (v1)      │           │   (v2)      │
       │  현재 운영   │           │  새 버전    │
       │   ← 트래픽   │           │             │
       └─────────────┘           └─────────────┘

                     스위치 전환!

       ┌─────────────┐           ┌─────────────┐
       │   Blue      │           │   Green     │
       │   (v1)      │           │   (v2)      │
       │  대기 상태   │           │  현재 운영   │
       │             │           │   ← 트래픽   │
       └─────────────┘           └─────────────┘
```

- **장점**: 즉각적인 롤백 가능 (다시 Blue로 전환), 배포 테스트 후 전환 가능
- **단점**: 동일한 환경 2개 필요 (비용 2배), 데이터베이스 스키마 변경 시 복잡

**Canary 배포**

새 버전을 **일부 사용자에게만 먼저 배포**하여 문제가 없는지 확인한 후, 점진적으로 확대한다. 카나리아 새가 광산에서 가스를 먼저 감지하는 것에서 유래했다.

```
1단계: 5% 트래픽만 새 버전으로
┌─────────────────────────────────────────────────┐
│  ████████████████████████████████████████ v1    │  95%
│  ██ v2                                          │   5% ← 카나리
└─────────────────────────────────────────────────┘

2단계: 문제없으면 25%로 확대
┌─────────────────────────────────────────────────┐
│  ██████████████████████████████████ v1          │  75%
│  ████████████ v2                                │  25%
└─────────────────────────────────────────────────┘

3단계: 50%로 확대
┌─────────────────────────────────────────────────┐
│  ████████████████████████ v1                    │  50%
│  ████████████████████████ v2                    │  50%
└─────────────────────────────────────────────────┘

최종: 100% 전환
┌─────────────────────────────────────────────────┐
│  ████████████████████████████████████████ v2    │ 100%
└─────────────────────────────────────────────────┘
```

- **장점**: 위험 최소화 (소수 사용자만 영향), 실제 트래픽으로 테스트 가능
- **단점**: 구현 복잡 (트래픽 분배 로직 필요), 모니터링 필수

| 전략 | 설명 | 장점 | 단점 | 적합한 경우 |
|------|------|------|------|------------|
| **Rolling** | 순차적 교체 | 추가 리소스 불필요 | 롤백 느림 | 일반적인 배포 |
| **Blue-Green** | 환경 전환 | 즉각 롤백 | 비용 2배 | 빠른 롤백 필요 |
| **Canary** | 점진적 확대 | 위험 최소화 | 구현 복잡 | 대규모 서비스 |

### ⭕ GitOps란? ◑

**Git을 인프라와 애플리케이션 배포의 단일 진실 소스(Single Source of Truth)**로 사용하는 방식이다.

```
기존 CI/CD:
개발자 → Git Push → CI → Build → CD → 수동/스크립트로 배포

GitOps:
개발자 → Git Push → CI → Build → 이미지 Push → Git Commit (매니페스트)
                                                      ↓
                                              GitOps 도구가 감지
                                                      ↓
                                              자동으로 K8s에 적용
```

| 원칙 | 설명 |
|------|------|
| **선언적 설정** | 모든 시스템 상태를 선언적으로 정의 |
| **Git이 진실의 소스** | 원하는 상태는 항상 Git에 저장 |
| **자동 동기화** | Git 상태와 실제 상태를 자동으로 일치시킴 |
| **지속적 조정** | 수동 변경을 감지하고 Git 상태로 복원 |

**GitOps 도구:**

| 도구 | 특징 |
|------|------|
| **ArgoCD** | Kubernetes 네이티브, 웹 UI 제공 |
| **Flux** | CNCF 프로젝트, 가벼움 |
| **Jenkins X** | Jenkins 기반 Kubernetes CI/CD |

**장점:**
- 모든 변경 이력이 Git에 기록 (감사 추적)
- Pull Request로 인프라 변경 리뷰
- 롤백이 쉬움 (`git revert`)
- 선언적이므로 드리프트(drift) 방지

---

### 서비스 메시 (Service Mesh) ◑

마이크로서비스 간 통신을 **인프라 레벨에서 관리**하는 계층이다.

```
서비스 메시 없이:                    서비스 메시 사용:

┌────────────┐                     ┌────────────────────────┐
│  Service A │                     │  Service A             │
│  ┌────────┐│                     │  ┌────────┐  ┌──────┐ │
│  │ 비즈니스 ││                     │  │ 비즈니스 │  │Sidecar││
│  │ 로직    ││                     │  │ 로직    │  │Proxy ││
│  │ +재시도 ││                     │  └────────┘  └──┬───┘ │
│  │ +서킷   ││                     └─────────────────┼─────┘
│  │ 브레이커││                                       │
│  │ +로깅  ││                     ┌─────────────────┼─────┐
│  └────────┘│                     │  Service B      │     │
└────────────┘                     │  ┌────────┐  ┌──▼───┐ │
                                   │  │ 비즈니스 │  │Sidecar││
                                   │  │ 로직    │  │Proxy ││
                                   │  └────────┘  └──────┘ │
                                   └────────────────────────┘

 애플리케이션에 통신 로직 포함           통신 로직을 Sidecar가 처리
```

**서비스 메시가 제공하는 기능:**

| 기능 | 설명 |
|------|------|
| **트래픽 관리** | 로드밸런싱, 라우팅, 카나리 배포 |
| **보안** | mTLS (서비스 간 암호화), 인증/인가 |
| **관찰성** | 분산 트레이싱, 메트릭, 로깅 |
| **회복성** | 재시도, 타임아웃, 서킷 브레이커 |

**대표 도구:**

| 도구 | 특징 |
|------|------|
| **Istio** | 가장 많이 사용, 기능 풍부, 학습 곡선 높음 |
| **Linkerd** | 가볍고 간단, Rust로 작성된 프록시 |
| **Consul Connect** | HashiCorp, 서비스 디스커버리 통합 |

---

## 6. 모니터링과 로깅 ◑

### 왜 모니터링이 필요한가? ◑

프로덕션 환경에서 **문제가 발생했을 때 빠르게 감지하고 원인을 파악**하기 위해 모니터링이 필수적이다.

- 서버가 다운되기 전에 CPU/메모리 사용량 급증을 감지
- API 응답 시간이 느려지는 것을 사용자 불만 전에 파악
- 에러 발생률 증가를 실시간으로 알림 받기
- 장애 발생 시 로그를 통해 원인 분석

모니터링은 크게 **메트릭 모니터링**, **로그 관리**, **분산 트레이싱** 세 가지로 나뉜다.

### 모니터링 도구 ◑

**Prometheus (메트릭 수집)**

시계열 데이터베이스로, 서버/애플리케이션의 **숫자 메트릭**을 수집한다. Pull 방식으로 주기적으로 대상 시스템에서 데이터를 가져온다.

- CPU 사용률, 메모리 사용량, 요청 수, 응답 시간 등
- PromQL이라는 쿼리 언어로 데이터 조회
- 알림 규칙 설정 가능 (Alertmanager와 연동)

**Grafana (시각화)**

Prometheus 등에서 수집한 데이터를 **대시보드로 시각화**한다. 그래프, 게이지, 테이블 등 다양한 형태로 메트릭을 표시하고, 알림을 설정할 수 있다.

```
┌────────────────────────────────────────────────────────────────┐
│                     Grafana 대시보드 예시                        │
├────────────────────────────────────────────────────────────────┤
│  CPU Usage          │  Memory Usage       │  Request Rate     │
│  ┌───────────────┐  │  ┌───────────────┐  │  ┌─────────────┐  │
│  │    ╱╲   ╱╲    │  │  │   ──────────  │  │  │  ▁▃▅▇█▇▅▃  │  │
│  │  ╱╲  ╲ ╱  ╲   │  │  │              │  │  │            │  │
│  │ ╱    ╳    ╲  │  │  │              │  │  │            │  │
│  └───────────────┘  │  └───────────────┘  │  └─────────────┘  │
│       45%           │       72%           │    1.2K req/s     │
└────────────────────────────────────────────────────────────────┘
```

**ELK Stack (로그 관리)**

로그를 수집, 저장, 검색하는 스택이다.

- **Elasticsearch**: 로그를 저장하고 검색하는 검색 엔진
- **Logstash**: 로그를 수집하고 파싱하여 Elasticsearch로 전송
- **Kibana**: 로그를 시각화하고 검색하는 웹 UI

에러 로그에서 특정 키워드를 검색하거나, 특정 시간대의 로그를 분석할 때 사용한다.

**Jaeger / Zipkin (분산 트레이싱)**

마이크로서비스 환경에서 **하나의 요청이 여러 서비스를 거치는 경로를 추적**한다. 어느 서비스에서 병목이 발생하는지, 에러가 어디서 시작됐는지 파악할 수 있다.

```
요청 흐름 추적 예시:

[사용자] → [API Gateway] → [User Service] → [Order Service] → [Payment Service]
              2ms              15ms             45ms ← 병목!       10ms

총 응답 시간: 72ms (Payment Service에서 대부분 소요)
```

### 핵심 메트릭 방법론 ◑

어떤 메트릭을 모니터링해야 할지 결정하는 두 가지 대표적인 방법론이 있다.

**RED 메트릭 (요청 중심)**

**사용자 관점**에서 서비스 상태를 파악한다. 주로 API 서버, 웹 서비스에 적합하다.

- **R**ate: 초당 요청 수 (얼마나 많은 요청이 오는가?)
- **E**rrors: 에러 발생률 (요청 중 몇 %가 실패하는가?)
- **D**uration: 응답 시간 (요청 처리에 얼마나 걸리는가?)

**USE 메트릭 (리소스 중심)**

**시스템 리소스 관점**에서 상태를 파악한다. 서버, 데이터베이스 등 인프라 모니터링에 적합하다.

- **U**tilization: 사용률 (리소스가 얼마나 바쁜가? CPU 80%)
- **S**aturation: 포화도 (처리 대기 중인 작업이 얼마나 있는가? 큐 길이)
- **E**rrors: 에러 수 (리소스 관련 에러가 발생하는가?)

| 방법론 | 관점 | 주요 메트릭 | 적용 대상 |
|--------|------|------------|----------|
| **RED** | 요청/사용자 | Rate, Errors, Duration | API, 웹 서비스 |
| **USE** | 리소스/시스템 | Utilization, Saturation, Errors | 서버, DB, 네트워크 |

---

## 면접 대비 체크리스트 ◑

- [ ] IaaS, PaaS, SaaS, FaaS 차이
- [ ] Docker vs VM
- [ ] Docker Image, Container, Dockerfile 개념
- [ ] Kubernetes 아키텍처 (Control Plane, Node 구성요소)
- [ ] Kubernetes 핵심 개념 (Pod, Service, Deployment, ReplicaSet)
- [ ] Service 유형 (ClusterIP, NodePort, LoadBalancer)
- [ ] Ingress의 역할과 라우팅 방식
- [ ] ConfigMap과 Secret 차이
- [ ] CI/CD 파이프라인
- [ ] IaC 개념 (선언적 vs 명령적)
- [ ] Terraform 워크플로우와 State 관리
- [ ] Terraform vs Ansible 차이
- [ ] Ansible 핵심 개념 (Playbook, Inventory, Role, 멱등성)
- [ ] 배포 전략 (Rolling, Blue-Green, Canary)
- [ ] GitOps 개념과 장점
- [ ] 서비스 메시 (Istio, Sidecar 패턴)
- [ ] 모니터링 (RED/USE 메트릭, Prometheus, Grafana)
