# 15. 컴퓨터 비전과 그래픽스 (Computer Vision & Graphics) ◑

---

## Part A: 컴퓨터 비전 (Computer Vision)

---

## 1. 컴퓨터 비전 기초 ◑

### 컴퓨터 비전이란?

컴퓨터가 디지털 이미지나 비디오를 이해하고 해석하는 분야이다.

### 주요 응용 분야 ◑

| 분야 | 예시 |
|------|------|
| 이미지 분류 | 개/고양이 분류, 제품 분류 |
| 객체 탐지 | 자율 주행, 보안 카메라 |
| 시맨틱 분할 | 의료 영상 분석, 자율 주행 |
| 얼굴 인식 | 잠금 해제, 출입 관리 |
| OCR | 문서 스캔, 차량 번호판 |
| 포즈 추정 | 모션 캡처, AR |

### 컴퓨터 비전 파이프라인 ◑

```
이미지 획득 → 전처리 → 특징 추출 → 모델 추론 → 후처리 → 결과
```

---

## 2. 이미지 처리 기초 ◑

### 디지털 이미지 표현 ◑

| 개념 | 설명 |
|------|------|
| **픽셀** | 이미지의 최소 단위 |
| **해상도** | 가로 × 세로 픽셀 수 |
| **채널** | 색상 구성 요소 (RGB: 3채널, RGBA: 4채널) |
| **비트 깊이** | 픽셀당 비트 수 (8비트: 0~255) |

### 색 공간 (Color Space) ◑

| 색 공간 | 설명 | 용도 |
|---------|------|------|
| **RGB** | 빨강, 초록, 파랑 | 디스플레이 |
| **BGR** | OpenCV 기본 순서 | OpenCV |
| **HSV** | 색상, 채도, 명도 | 색상 기반 처리 |
| **Grayscale** | 밝기만 표현 | 전처리, 엣지 검출 |
| **YCbCr** | 휘도 + 색차 | 영상 압축 (JPEG) |

### 기본 이미지 처리 연산 ◑

| 연산 | 설명 |
|------|------|
| **리사이징** | 크기 변환 (보간법: Bilinear, Bicubic) |
| **크롭** | 관심 영역 추출 |
| **회전/변환** | 어파인 변환, 원근 변환 |
| **정규화** | 픽셀 값 범위 조정 (0~1 또는 -1~1) |
| **히스토그램 균등화** | 대비 향상 |

### 필터링 (Convolution) ◑

| 필터 | 용도 |
|------|------|
| **Gaussian Blur** | 노이즈 제거, 스무딩 |
| **Sobel** | 엣지 검출 (방향성) |
| **Laplacian** | 엣지 검출 (2차 미분) |
| **Canny** | 엣지 검출 (복합 알고리즘) |
| **Sharpening** | 선명도 향상 |

---

## 3. CNN (Convolutional Neural Network) ◑

### ⭕ CNN이란?

이미지의 공간적 특징을 학습하기 위해 합성곱 연산을 사용하는 신경망이다.

### CNN의 핵심 구성 요소 ◑

| 레이어 | 역할 |
|--------|------|
| **Convolution** | 필터로 특징 추출 (엣지 → 텍스처 → 패턴 → 객체) |
| **Activation (ReLU)** | 비선형성 추가 |
| **Pooling** | 공간 크기 축소, 위치 불변성 |
| **Fully Connected** | 분류를 위한 최종 레이어 |
| **Softmax** | 확률 분포 출력 |

### ⭕ 합성곱 연산의 핵심 개념

| 개념 | 설명 |
|------|------|
| **Kernel/Filter** | 학습되는 가중치 행렬 (예: 3×3) |
| **Stride** | 필터 이동 간격 |
| **Padding** | 출력 크기 유지를 위한 경계 패딩 |
| **Feature Map** | 합성곱 결과 |

**출력 크기 계산:**
```
Output = (Input - Kernel + 2×Padding) / Stride + 1
```

### ⭕ Pooling의 역할

| 종류 | 동작 |
|------|------|
| **Max Pooling** | 영역 내 최대값 선택 |
| **Average Pooling** | 영역 내 평균값 |
| **Global Average Pooling** | 전체 feature map을 하나의 값으로 |

**장점:**
- 계산량 감소
- 위치 변화에 대한 불변성 (Translation Invariance)
- 과적합 방지

### 주요 CNN 아키텍처 ◑

| 모델 | 연도 | 특징 |
|------|------|------|
| **LeNet** | 1998 | 최초의 성공적인 CNN |
| **AlexNet** | 2012 | ImageNet 우승, Deep CNN 시대 개막 |
| **VGGNet** | 2014 | 3×3 필터 반복, 깊은 네트워크 |
| **GoogLeNet** | 2014 | Inception 모듈, 1×1 합성곱 |
| **ResNet** | 2015 | Skip Connection, 깊은 학습 가능 |
| **EfficientNet** | 2019 | 효율적인 스케일링 |

### ⭕ ResNet의 Skip Connection이란?

입력을 출력에 직접 더하여 그래디언트 소실 문제를 해결한다.

```
x → Conv → ReLU → Conv → (+) → ReLU → output
    └────────────────────┘
         identity
```

---

## 4. 객체 탐지 (Object Detection) ◑

### ⭕ 객체 탐지란?

이미지에서 객체의 위치(Bounding Box)와 클래스를 동시에 예측하는 작업이다.

### 주요 객체 탐지 모델 ◑

| 계열 | 모델 | 특징 |
|------|------|------|
| **Two-Stage** | R-CNN → Fast R-CNN → Faster R-CNN | Region Proposal 후 분류, 정확도 높음 |
| **One-Stage** | YOLO, SSD, RetinaNet | 한 번에 예측, 속도 빠름 |
| **Transformer** | DETR | End-to-End, Transformer 기반 |

### YOLO (You Only Look Once) ◑

| 특징 | 설명 |
|------|------|
| 그리드 분할 | 이미지를 S×S 그리드로 분할 |
| 동시 예측 | 바운딩 박스, 신뢰도, 클래스 확률 |
| 실시간 처리 | 높은 FPS (실시간 가능) |

### 평가 지표 ◑

| 지표 | 설명 |
|------|------|
| **IoU** | Intersection over Union, 박스 겹침 정도 |
| **Precision** | 예측 중 정답 비율 |
| **Recall** | 정답 중 예측된 비율 |
| **mAP** | Mean Average Precision, 클래스별 AP 평균 |

---

## 5. 시맨틱 분할과 기타 태스크 ◑

### 시맨틱 분할 (Semantic Segmentation) ◑

모든 픽셀에 클래스 레이블을 할당한다.

| 모델 | 특징 |
|------|------|
| **FCN** | Fully Convolutional, 최초의 분할 네트워크 |
| **U-Net** | 인코더-디코더, Skip Connection |
| **DeepLab** | Atrous Convolution, CRF |

### Instance vs Semantic vs Panoptic ◑

| 유형 | 설명 |
|------|------|
| **Semantic** | 클래스별 분할 (같은 클래스 구분 X) |
| **Instance** | 객체별 분할 (같은 클래스도 구분) |
| **Panoptic** | Semantic + Instance |

### Vision Transformer (ViT) ◑

| 특징 | 설명 |
|------|------|
| 패치 기반 | 이미지를 패치로 분할 후 시퀀스로 처리 |
| Self-Attention | 전역적 관계 학습 |
| 대용량 데이터 | 많은 데이터에서 CNN 능가 |

---

## Part B: 컴퓨터 그래픽스 (Computer Graphics)

---

## 6. 컴퓨터 그래픽스 기초 ◑

### 컴퓨터 그래픽스란?

컴퓨터를 이용하여 이미지를 생성하고 조작하는 분야이다.

### 주요 분야 ◑

| 분야 | 설명 |
|------|------|
| **렌더링** | 3D 장면을 2D 이미지로 변환 |
| **모델링** | 3D 객체 형상 생성 |
| **애니메이션** | 시간에 따른 변화 표현 |
| **시뮬레이션** | 물리 현상 재현 |

---

## 7. 렌더링 파이프라인 ◑

### ⭕ 그래픽스 렌더링 파이프라인이란?

3D 장면을 2D 화면에 표시하기 위한 처리 단계이다.

```
정점 데이터 → Vertex Shader → 래스터화 → Fragment Shader → 프레임버퍼
                    ↓
            (테셀레이션, 지오메트리 셰이더 - 선택적)
```

### 파이프라인 단계 ◑

| 단계 | 역할 |
|------|------|
| **Input Assembly** | 정점/인덱스 데이터 수집 |
| **Vertex Shader** | 정점 변환 (위치, 조명 계산) |
| **Tessellation** | 폴리곤 세분화 (선택적) |
| **Geometry Shader** | 프리미티브 생성/제거 (선택적) |
| **Rasterization** | 프리미티브를 프래그먼트로 변환 |
| **Fragment Shader** | 픽셀 색상 계산 (텍스처, 조명) |
| **Output Merger** | 깊이/스텐실 테스트, 블렌딩 |

### 좌표 변환 파이프라인 ◑

```
Local Space (모델 좌표)
    ↓ Model Matrix
World Space (월드 좌표)
    ↓ View Matrix
View Space (카메라 좌표)
    ↓ Projection Matrix
Clip Space (클립 좌표)
    ↓ Perspective Division
NDC (Normalized Device Coordinates)
    ↓ Viewport Transform
Screen Space (화면 좌표)
```

---

## 8. 셰이더 (Shader) ◑

### ⭕ 셰이더란?

GPU에서 실행되는 작은 프로그램으로, 렌더링 과정을 커스터마이징한다.

### 셰이더 종류 ◑

| 셰이더 | 역할 | 입력 → 출력 |
|--------|------|-------------|
| **Vertex Shader** | 정점 변환, 조명 전처리 | 정점 → 정점 |
| **Fragment/Pixel Shader** | 픽셀 색상 결정 | 보간된 데이터 → 색상 |
| **Geometry Shader** | 프리미티브 조작 | 프리미티브 → 프리미티브들 |
| **Compute Shader** | 범용 GPU 연산 | 데이터 → 데이터 |

### 셰이딩 언어 ◑

| 언어 | API |
|------|-----|
| **GLSL** | OpenGL, Vulkan |
| **HLSL** | DirectX |
| **Metal SL** | Metal (Apple) |
| **WGSL** | WebGPU |

### 간단한 셰이더 예시 (GLSL) ◑

```glsl
// Vertex Shader
#version 330 core
layout (location = 0) in vec3 aPos;
uniform mat4 MVP;

void main() {
    gl_Position = MVP * vec4(aPos, 1.0);
}

// Fragment Shader
#version 330 core
out vec4 FragColor;

void main() {
    FragColor = vec4(1.0, 0.5, 0.2, 1.0);
}
```

---

## 9. 조명과 셰이딩 모델 ◑

### Phong 조명 모델 ◑

| 구성 요소 | 설명 |
|-----------|------|
| **Ambient** | 환경광, 전체적인 밝기 |
| **Diffuse** | 확산광, 표면 각도에 따른 밝기 |
| **Specular** | 반사광, 하이라이트 |

```
I = Ambient + Diffuse + Specular
  = ka·Ia + kd·(L·N)·Id + ks·(R·V)^n·Is
```

### Phong vs Gouraud 셰이딩 ◑

| 방식 | 계산 위치 | 품질 | 성능 |
|------|----------|------|------|
| **Gouraud** | 정점에서 조명, 보간 | 낮음 | 빠름 |
| **Phong** | 픽셀에서 조명 | 높음 | 느림 |

### PBR (Physically Based Rendering) ◑

물리적으로 정확한 조명 모델:

| 파라미터 | 설명 |
|----------|------|
| **Albedo** | 기본 색상 |
| **Metallic** | 금속성 (0: 비금속, 1: 금속) |
| **Roughness** | 거칠기 (0: 매끄러움, 1: 거침) |
| **Normal** | 표면 법선 (노멀맵) |

---

## 10. 텍스처와 매핑 ◑

### 텍스처 종류 ◑

| 텍스처 | 용도 |
|--------|------|
| **Diffuse/Albedo** | 기본 색상 |
| **Normal Map** | 표면 디테일 (범프) |
| **Specular Map** | 반사 강도 |
| **Height Map** | 높이 정보 |
| **AO Map** | Ambient Occlusion |

### UV 매핑 ◑

3D 표면을 2D 텍스처 좌표로 매핑:

```
(x, y, z) → (u, v)
```

| 기법 | 설명 |
|------|------|
| **Planar** | 평면 투영 |
| **Cylindrical** | 원통 투영 |
| **Spherical** | 구 투영 |
| **UV Unwrap** | 수동/자동 전개 |

---

## 11. 그래픽스 API ◑

### 주요 API 비교 ◑

| API | 특징 | 플랫폼 |
|-----|------|--------|
| **OpenGL** | 크로스 플랫폼, 레거시 | 다양한 OS |
| **Vulkan** | 저수준, 고성능 | 크로스 플랫폼 |
| **DirectX 12** | Windows 최적화 | Windows, Xbox |
| **Metal** | Apple 최적화 | macOS, iOS |
| **WebGL/WebGPU** | 브라우저 | 웹 |

### 렌더링 기법 ◑

| 기법 | 설명 |
|------|------|
| **래스터화** | 실시간 렌더링의 기본 (삼각형 → 픽셀) |
| **레이 트레이싱** | 광선 추적, 사실적인 반사/굴절 |
| **패스 트레이싱** | 전역 조명, 가장 사실적 |

---

## 면접 대비 체크리스트 ◑

### 컴퓨터 비전
- [ ] CNN의 구성 요소 (Convolution, Pooling, FC)
- [ ] 합성곱 연산과 출력 크기 계산
- [ ] Pooling의 역할과 종류
- [ ] ResNet Skip Connection
- [ ] YOLO 등 객체 탐지 모델 특징
- [ ] IoU, mAP 평가 지표
- [ ] Semantic vs Instance Segmentation

### 컴퓨터 그래픽스
- [ ] 렌더링 파이프라인 단계
- [ ] 좌표 변환 (Model, View, Projection)
- [ ] Vertex Shader vs Fragment Shader
- [ ] Phong 조명 모델 (Ambient, Diffuse, Specular)
- [ ] 텍스처 매핑과 UV 좌표
- [ ] 래스터화 vs 레이 트레이싱
