# 17. Git과 버전 관리 (Git & Version Control) ◑

---

## 1. 버전 관리 시스템 ◑

### 버전 관리란?

파일의 변경 이력을 기록하고 관리하는 시스템이다.

### VCS 유형 ◑

| 유형 | 설명 | 예시 |
|------|------|------|
| **로컬 VCS** | 로컬에서만 버전 관리 | RCS |
| **중앙집중식 (CVCS)** | 중앙 서버에 저장소 | SVN, CVS |
| **분산형 (DVCS)** | 각 클라이언트가 전체 저장소 복제 | Git, Mercurial |

### ⭕ Git을 사용하는 이유는?

| 특징 | 설명 |
|------|------|
| **분산형** | 오프라인 작업 가능, 서버 장애에 강함 |
| **빠른 속도** | 대부분 로컬 연산 |
| **브랜치 경량화** | 브랜치 생성/병합이 빠르고 쉬움 |
| **무결성** | SHA-1 해시로 데이터 무결성 보장 |
| **스테이징 영역** | 커밋 전 변경사항 선별 가능 |

---

## 2. Git 기본 개념 ◑

### Git의 세 가지 영역 ◑

```
Working Directory    →    Staging Area    →    Repository
   (작업 디렉터리)           (스테이징)            (저장소)
        │                      │                    │
        │      git add         │     git commit     │
        └──────────────────────┴────────────────────┘
```

| 영역 | 설명 |
|------|------|
| **Working Directory** | 실제 작업하는 파일들 |
| **Staging Area (Index)** | 커밋할 변경사항을 준비하는 영역 |
| **Repository (.git)** | 커밋 이력이 저장되는 곳 |

### 파일 상태 ◑

```
Untracked → Staged → Committed
               ↑         │
               └─ Modified ←┘
```

| 상태 | 설명 |
|------|------|
| **Untracked** | Git이 추적하지 않는 새 파일 |
| **Staged** | 다음 커밋에 포함될 변경사항 |
| **Committed** | 저장소에 안전하게 저장됨 |
| **Modified** | 커밋 후 변경되었지만 스테이징 안 됨 |

### Git 객체 ◑

| 객체 | 설명 |
|------|------|
| **Blob** | 파일 내용 저장 |
| **Tree** | 디렉터리 구조 (blob과 tree 참조) |
| **Commit** | 메타데이터 + tree 참조 + 부모 커밋 참조 |
| **Tag** | 특정 커밋에 대한 레이블 |

---

## 3. 기본 명령어 ◑

### 저장소 관련

```bash
git init                    # 새 저장소 생성
git clone <url>             # 원격 저장소 복제
```

### 변경사항 관리

```bash
git status                  # 상태 확인
git add <file>              # 스테이징
git add .                   # 모든 변경사항 스테이징
git commit -m "message"     # 커밋
git commit --amend          # 마지막 커밋 수정
```

### 이력 조회

```bash
git log                     # 커밋 이력
git log --oneline --graph   # 간단히 그래프로
git diff                    # 변경사항 비교
git diff --staged           # 스테이징된 변경사항
git show <commit>           # 특정 커밋 상세
```

### 되돌리기

```bash
git checkout -- <file>      # 작업 디렉터리 변경 취소
git restore <file>          # (Git 2.23+) 위와 동일
git reset HEAD <file>       # 스테이징 취소
git reset --soft HEAD~1     # 커밋 취소, 변경사항 유지
git reset --hard HEAD~1     # 커밋 취소, 변경사항 삭제 (주의!)
git revert <commit>         # 커밋 되돌리는 새 커밋 생성
```

### ⭕ reset과 revert의 차이는? ◑

| 명령 | 동작 | 이력 | 사용 시점 |
|------|------|------|----------|
| **reset** | 커밋을 삭제하고 되돌림 | 이력 변경 | 로컬에서만 |
| **revert** | 되돌리는 새 커밋 생성 | 이력 보존 | 공유된 브랜치 |

---

## 4. 브랜치 ◑

### 브랜치 기본 명령어

```bash
git branch                  # 브랜치 목록
git branch <name>           # 브랜치 생성
git checkout <name>         # 브랜치 전환
git switch <name>           # (Git 2.23+) 브랜치 전환
git checkout -b <name>      # 생성 + 전환
git branch -d <name>        # 브랜치 삭제
git branch -D <name>        # 강제 삭제
```

### 병합 (Merge)

```bash
git merge <branch>          # 현재 브랜치에 병합
git merge --no-ff <branch>  # Fast-forward 방지 (병합 커밋 생성)
```

### ⭕ Fast-forward vs 3-way Merge ◑

```
Fast-forward (선형 이력):
main:     A ─ B
               ↘
feature:        C ─ D
                    ↓
결과:     A ─ B ─ C ─ D (main이 앞으로 이동)

3-way Merge (분기 이력):
main:     A ─ B ─ E
               ↘   ↘
feature:        C ─ D ─ M (병합 커밋)
```

| 방식 | 조건 | 특징 |
|------|------|------|
| **Fast-forward** | main에 추가 커밋 없음 | 깔끔, 선형 이력 |
| **3-way Merge** | 양쪽 모두 커밋 있음 | 병합 커밋 생성 |

### Rebase ◑

```bash
git rebase <branch>         # 현재 브랜치를 대상 브랜치 위로 재배치
git rebase -i HEAD~3        # 인터랙티브 리베이스 (커밋 정리)
```

### ⭕ Merge vs Rebase ◑

```
Merge:
main:     A ─ B ─ C ─ ─ ─ ─ M
               ↘           ↗
feature:        D ─ E ─ F ─┘

Rebase:
main:     A ─ B ─ C
                   ↘
feature:            D' ─ E' ─ F' (커밋 재작성)
```

| 방식 | 장점 | 단점 | 사용 시점 |
|------|------|------|----------|
| **Merge** | 이력 보존, 안전 | 복잡한 이력 | 공유 브랜치 |
| **Rebase** | 깔끔한 선형 이력 | 이력 재작성 | 로컬/개인 브랜치 |

**주의**: 공유된 브랜치에서는 rebase 사용 금지 (이력 충돌 발생)

---

## 5. 원격 저장소 ◑

### 원격 관련 명령어

```bash
git remote -v                       # 원격 저장소 목록
git remote add origin <url>         # 원격 추가
git fetch origin                    # 원격 변경사항 가져오기 (병합 X)
git pull origin main                # fetch + merge
git pull --rebase origin main       # fetch + rebase
git push origin main                # 원격에 푸시
git push -u origin main             # 업스트림 설정 + 푸시
git push --force                    # 강제 푸시 (주의!)
```

### ⭕ fetch와 pull의 차이는? ◑

| 명령 | 동작 |
|------|------|
| **fetch** | 원격 변경사항만 가져옴 (로컬 작업 영향 없음) |
| **pull** | fetch + merge (자동 병합) |

---

## 6. 충돌 해결 ◑

### 충돌 발생 시

```
<<<<<<< HEAD
현재 브랜치의 내용
=======
병합하려는 브랜치의 내용
>>>>>>> feature
```

### 충돌 해결 과정

```bash
# 1. 충돌 파일 수동 편집
# 2. 충돌 마커 제거 후 원하는 내용으로 수정
# 3. 스테이징 및 커밋
git add <file>
git commit
```

---

## 7. Git 워크플로우 ◑

### ⭕ Git Flow ◑

```
main ─────────────────────────────────────→ (배포)
  ↑                   ↑
  └── release ────────┘
        ↑
develop ──┬───────────────────────→ (개발 통합)
          ↑           ↑
          └── feature ┘
                ↑
             hotfix ─→ main (긴급 수정)
```

| 브랜치 | 역할 |
|--------|------|
| **main** | 배포 가능한 안정 버전 |
| **develop** | 개발 통합 브랜치 |
| **feature/** | 기능 개발 |
| **release/** | 배포 준비 |
| **hotfix/** | 긴급 버그 수정 |

**장점**: 체계적, 대규모 팀에 적합
**단점**: 복잡함, 브랜치 관리 비용

### GitHub Flow ◑

```
main ─────────────────────────────→ (항상 배포 가능)
  ↑           ↑           ↑
  └─ feature ─┴─ feature ─┘
       ↓
    Pull Request → 코드 리뷰 → Merge
```

| 원칙 | 설명 |
|------|------|
| main은 항상 배포 가능 | 테스트 통과된 코드만 |
| feature 브랜치에서 작업 | main에서 분기 |
| Pull Request로 리뷰 | 병합 전 코드 리뷰 |
| 병합 후 즉시 배포 | CI/CD 연동 |

**장점**: 단순, CI/CD에 적합
**단점**: 복잡한 배포 주기에 부적합

### Trunk-Based Development ◑

```
main (trunk) ──────────────────────→
  ↑     ↑     ↑
  └─────┴─────┘
  (짧은 브랜치, 자주 통합)
```

| 특징 | 설명 |
|------|------|
| 짧은 수명의 브랜치 | 1-2일 내 병합 |
| 작은 단위 커밋 | 자주 통합 |
| Feature Flag | 미완성 기능 숨김 |

**장점**: 통합 충돌 최소화, 빠른 피드백
**단점**: Feature Flag 관리 필요

---

## 8. 고급 기능 ◑

### Stash ◑

작업 중인 변경사항을 임시 저장한다.

```bash
git stash                   # 임시 저장
git stash list              # 스태시 목록
git stash pop               # 복원 + 삭제
git stash apply             # 복원 (삭제 안 함)
git stash drop              # 삭제
```

### Cherry-pick ◑

특정 커밋만 현재 브랜치에 적용한다.

```bash
git cherry-pick <commit>    # 특정 커밋 적용
```

### Bisect ◑

이진 탐색으로 버그가 도입된 커밋을 찾는다.

```bash
git bisect start
git bisect bad              # 현재가 버그 있음
git bisect good <commit>    # 이 커밋은 정상
# Git이 중간 커밋으로 체크아웃, good/bad 반복
git bisect reset            # 종료
```

### Submodule ◑

저장소 안에 다른 저장소를 포함한다.

```bash
git submodule add <url>
git submodule update --init --recursive
```

### Git Hooks ◑

특정 이벤트 시 스크립트를 실행한다.

| 훅 | 시점 |
|-----|------|
| **pre-commit** | 커밋 전 (린트, 테스트) |
| **commit-msg** | 커밋 메시지 검증 |
| **pre-push** | 푸시 전 |
| **post-merge** | 병합 후 |

---

## 9. 좋은 커밋 작성법 ◑

### 커밋 메시지 컨벤션

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Conventional Commits ◑

| 타입 | 설명 |
|------|------|
| **feat** | 새 기능 |
| **fix** | 버그 수정 |
| **docs** | 문서 변경 |
| **style** | 포맷팅 (코드 변경 없음) |
| **refactor** | 리팩토링 |
| **test** | 테스트 추가/수정 |
| **chore** | 빌드, 설정 변경 |

### 좋은 커밋의 조건 ◑

- 작고 원자적 (하나의 논리적 변경)
- 명확한 메시지 (무엇을, 왜)
- 빌드/테스트 통과 상태

---

## 10. .gitignore ◑

### 문법

```gitignore
# 주석
*.log           # 모든 .log 파일
/build          # 루트의 build 디렉터리
build/          # 모든 build 디렉터리
!important.log  # 예외 (추적함)
**/temp         # 모든 위치의 temp
```

### 일반적으로 무시하는 파일

```gitignore
# 의존성
node_modules/
vendor/
.venv/

# 빌드 결과물
build/
dist/
*.exe
*.dll

# 환경 설정
.env
*.local

# IDE
.idea/
.vscode/
*.swp

# OS
.DS_Store
Thumbs.db
```

---

## 면접 대비 체크리스트 ◑

- [ ] Git의 세 가지 영역 (Working Directory, Staging, Repository)
- [ ] reset vs revert 차이
- [ ] Fast-forward vs 3-way Merge
- [ ] Merge vs Rebase 차이와 사용 시점
- [ ] fetch vs pull 차이
- [ ] Git Flow vs GitHub Flow vs Trunk-Based
- [ ] 충돌 해결 과정
- [ ] Stash, Cherry-pick 사용법
- [ ] 좋은 커밋 메시지 작성법
