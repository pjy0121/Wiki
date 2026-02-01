# 17. Git과 버전 관리 (Git & Version Control) ◑

---

## 1. 버전 관리 시스템 ◑

### 버전 관리란?

버전 관리 시스템(VCS, Version Control System)은 파일의 변경 이력을 기록하고 관리하는 시스템이다. 소프트웨어 개발에서 코드의 변경 사항을 추적하고, 필요할 때 이전 버전으로 돌아갈 수 있게 해준다.

버전 관리가 필요한 이유:

1. **변경 이력 추적**: 누가, 언제, 무엇을, 왜 변경했는지 기록
2. **협업 지원**: 여러 개발자가 동시에 작업 가능
3. **백업 및 복구**: 실수로 삭제하거나 잘못된 변경 복구
4. **브랜치**: 독립적인 개발 라인에서 실험적 기능 개발 가능

### VCS 유형 ◑

버전 관리 시스템은 세 가지 유형으로 발전해왔다.

```
[로컬 VCS]
개발자 PC
┌─────────────────────┐
│  Working Directory  │
│         ↓          │
│    Version DB       │
│   ┌───────────┐    │
│   │ v1 → v2 → v3   │
│   └───────────┘    │
└─────────────────────┘

[중앙집중식 VCS (CVCS)]
              중앙 서버
             ┌────────┐
             │  v1    │
             │  v2    │
             │  v3    │
             └───┬────┘
        ┌────────┼────────┐
        ↓        ↓        ↓
      개발자A  개발자B   개발자C
      (체크아웃) (체크아웃) (체크아웃)

[분산형 VCS (DVCS)]
    원격 서버              개발자A             개발자B
   ┌────────┐          ┌────────┐          ┌────────┐
   │ v1→v2→v3│ ←────→  │ v1→v2→v3│ ←────→  │ v1→v2→v3│
   └────────┘   push   └────────┘   push   └────────┘
                pull     (전체    pull       (전체
                         복제본)             복제본)
```

| 유형 | 설명 | 장점 | 단점 | 예시 |
|------|------|------|------|------|
| **로컬 VCS** | 로컬에서만 버전 관리 | 단순함 | 협업 불가, 백업 취약 | RCS |
| **중앙집중식 (CVCS)** | 중앙 서버에 저장소 | 관리 용이, 권한 제어 | 서버 의존, 단일 장애점 | SVN, CVS |
| **분산형 (DVCS)** | 각 클라이언트가 전체 저장소 복제 | 오프라인 작업, 빠름 | 학습 곡선 높음 | Git, Mercurial |

### ⭕ Git을 사용하는 이유는?

Git이 현재 가장 널리 사용되는 버전 관리 시스템인 이유:

| 특징 | 설명 |
|------|------|
| **분산형 구조** | 모든 개발자가 전체 이력을 가지고 있어 오프라인 작업 가능, 서버 장애에도 복구 용이 |
| **빠른 속도** | 대부분의 작업이 로컬에서 수행되어 네트워크 지연 없음. 커밋, 브랜치, 병합이 매우 빠름 |
| **브랜치 경량화** | 브랜치가 단순한 포인터이므로 생성/전환이 거의 즉각적. 실험적 개발이 자유로움 |
| **데이터 무결성** | 모든 객체가 SHA-1 해시로 식별되어 데이터 손상이나 변조 감지 가능 |
| **스테이징 영역** | 커밋 전에 변경사항을 선별적으로 준비할 수 있어 논리적인 커밋 단위 구성 가능 |
| **비선형 개발** | 여러 브랜치에서 병렬 개발 후 다양한 방식(merge, rebase)으로 통합 가능 |

Git의 역사: Git은 2005년 Linux 커널 개발자인 Linus Torvalds가 만들었다. 기존에 사용하던 BitKeeper가 무료 사용을 중단하면서, 빠르고 분산된 버전 관리 시스템의 필요성에 의해 탄생했다.

---

## 2. Git 기본 개념 ◑

### Git의 세 가지 영역 ◑

Git은 파일을 세 가지 영역에서 관리한다. 이 개념을 이해하는 것이 Git 사용의 핵심이다.

```
┌─────────────────────────────────────────────────────────────────────┐
│                        Working Directory                            │
│                     (실제 프로젝트 파일들)                           │
│                                                                      │
│   file1.js (modified)                                               │
│   file2.js (untracked)                                              │
│   file3.js (unchanged)                                              │
└────────────────────────────────┬────────────────────────────────────┘
                                 │
                          git add file1.js
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────────┐
│                        Staging Area (Index)                         │
│                    (다음 커밋에 포함될 스냅샷)                         │
│                                                                      │
│   file1.js (staged)                                                 │
└────────────────────────────────┬────────────────────────────────────┘
                                 │
                           git commit
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────────┐
│                        Repository (.git)                            │
│                      (커밋 이력이 저장됨)                            │
│                                                                      │
│   commit abc123 ← commit def456 ← commit 789ghi (HEAD)              │
└─────────────────────────────────────────────────────────────────────┘
```

| 영역 | 설명 | 위치 |
|------|------|------|
| **Working Directory** | 실제로 파일을 편집하는 프로젝트 디렉터리 | 프로젝트 폴더 |
| **Staging Area (Index)** | 다음 커밋에 포함될 변경사항을 준비하는 임시 영역 | `.git/index` 파일 |
| **Repository** | 모든 커밋 이력과 메타데이터가 저장되는 Git 데이터베이스 | `.git/` 디렉터리 |

스테이징 영역이 있는 이유: 작업 중인 여러 변경사항 중 일부만 선택적으로 커밋할 수 있다. 예를 들어, 버그 수정과 새 기능 개발을 동시에 했다면, 버그 수정만 먼저 커밋하고 새 기능은 나중에 커밋할 수 있다.

### 파일 상태 ◑

Git에서 추적하는 파일은 네 가지 상태 중 하나에 있다.

```
                    ┌────────────────────────────────────┐
                    │                                    │
                    ▼                                    │
┌──────────────┐  git add  ┌──────────┐  git commit  ┌──────────┐
│  Untracked   │ ────────→ │  Staged  │ ───────────→ │Committed │
│   (새 파일)   │           │ (스테이징) │              │ (저장됨)  │
└──────────────┘           └──────────┘              └────┬─────┘
                                ▲                         │
                                │                         │ 파일 수정
                                │                         ▼
                                │                   ┌──────────┐
                                └─────────────────  │ Modified │
                                     git add        │ (변경됨)  │
                                                    └──────────┘
```

| 상태 | 설명 | `git status` 표시 |
|------|------|------------------|
| **Untracked** | Git이 추적하지 않는 새 파일 | `Untracked files:` |
| **Staged** | 스테이징 영역에 추가됨, 다음 커밋에 포함 예정 | `Changes to be committed:` |
| **Committed** | 저장소에 안전하게 저장됨 | (표시 없음) |
| **Modified** | 커밋된 파일이 변경됨, 아직 스테이징 안 됨 | `Changes not staged for commit:` |

### Git 객체 모델 ◑

Git은 내부적으로 네 가지 객체 타입으로 모든 데이터를 저장한다. 모든 객체는 SHA-1 해시(40자리 16진수)로 식별된다.

```
[Commit 객체]                    [Tree 객체]                [Blob 객체]
┌───────────────────┐           ┌──────────────────┐      ┌─────────────┐
│ tree: abc123...   │ ────────→ │ blob: src/app.js │ ───→ │ (파일 내용)  │
│ parent: def456... │           │ blob: README.md  │      │ function... │
│ author: Kim       │           │ tree: src/       │      └─────────────┘
│ message: "Add..." │           └──────────────────┘
└───────────────────┘                   │
                                        ▼
                                 ┌──────────────────┐
                                 │ (하위 디렉터리)    │
                                 │ blob: index.js   │
                                 └──────────────────┘
```

| 객체 | 역할 | 내용 |
|------|------|------|
| **Blob** | 파일 내용 저장 | 파일의 실제 데이터 (파일명 미포함) |
| **Tree** | 디렉터리 구조 표현 | Blob과 다른 Tree에 대한 참조 + 파일명 |
| **Commit** | 스냅샷 저장 | Tree 참조 + 부모 커밋 + 작성자 + 메시지 |
| **Tag** | 특정 커밋에 레이블 | 커밋 참조 + 태그 이름 + 메시지 (Annotated Tag) |

중요한 점: Git은 파일의 "차이(diff)"를 저장하는 것이 아니라, 각 커밋 시점의 전체 "스냅샷"을 저장한다. 같은 내용의 파일은 같은 해시값을 가지므로, 실제로는 중복 저장되지 않는다 (Content-Addressable Storage).

### HEAD, 브랜치, 태그 ◑

```
[브랜치와 HEAD의 관계]

                    main (브랜치 포인터)
                      │
                      ▼
   A ─── B ─── C ─── D ← HEAD (현재 위치)
              │
              └─── E ─── F
                        ▲
                        │
                    feature (브랜치 포인터)

브랜치 = 특정 커밋을 가리키는 포인터 (파일 크기: 41바이트)
HEAD = 현재 작업 중인 브랜치를 가리키는 포인터
```

| 개념 | 설명 | 저장 위치 |
|------|------|----------|
| **HEAD** | 현재 체크아웃된 커밋 또는 브랜치를 가리킴 | `.git/HEAD` |
| **Branch** | 특정 커밋을 가리키는 이동 가능한 포인터 | `.git/refs/heads/` |
| **Tag** | 특정 커밋을 가리키는 고정된 포인터 (버전 릴리스용) | `.git/refs/tags/` |

```bash
# HEAD 확인
cat .git/HEAD
# 출력: ref: refs/heads/main

# 브랜치가 가리키는 커밋 확인
cat .git/refs/heads/main
# 출력: abc123def456... (커밋 해시)
```

---

## 3. 기본 명령어 ◑

### 저장소 생성 및 복제 ◑

```bash
# 새 저장소 생성 (현재 디렉터리에 .git 생성)
git init

# 특정 디렉터리에 저장소 생성
git init my-project

# 원격 저장소 복제
git clone https://github.com/user/repo.git

# 특정 브랜치만 복제
git clone -b develop https://github.com/user/repo.git

# 얕은 복제 (최신 커밋만, CI/CD에서 유용)
git clone --depth 1 https://github.com/user/repo.git
```

### 변경사항 관리 ◑

```bash
# 상태 확인
git status
git status -s  # 간략하게 (M: Modified, A: Added, ??: Untracked)

# 스테이징
git add file.js           # 특정 파일
git add src/              # 특정 디렉터리
git add .                 # 현재 디렉터리의 모든 변경
git add -A                # 전체 저장소의 모든 변경
git add -p                # 변경사항을 청크별로 선택적 추가 (대화형)

# 커밋
git commit -m "메시지"               # 인라인 메시지
git commit                            # 에디터에서 메시지 작성
git commit -a -m "메시지"            # add + commit (추적 중인 파일만)
git commit --amend                    # 마지막 커밋 수정
git commit --amend --no-edit         # 메시지 변경 없이 파일만 추가
```

`git add -p`의 유용성: 하나의 파일에서 여러 변경을 했을 때, 관련 있는 변경만 선택적으로 스테이징할 수 있다. 논리적인 커밋 단위를 만드는 데 매우 유용하다.

### 이력 조회 ◑

```bash
# 커밋 이력
git log                              # 전체 로그
git log --oneline                    # 한 줄씩 간략히
git log --graph                      # 브랜치 그래프
git log --oneline --graph --all     # 모든 브랜치의 그래프
git log -n 5                         # 최근 5개만
git log --author="Kim"               # 특정 작성자
git log --since="2024-01-01"         # 날짜 이후
git log -- file.js                   # 특정 파일 이력
git log -p                           # diff도 함께 표시
git log --stat                       # 변경된 파일 통계

# 변경사항 비교
git diff                             # Working Directory vs Staging
git diff --staged                    # Staging vs 마지막 커밋
git diff HEAD                        # Working Directory vs 마지막 커밋
git diff branch1..branch2            # 두 브랜치 비교
git diff abc123..def456              # 두 커밋 비교
git diff --name-only                 # 변경된 파일명만

# 특정 커밋 상세
git show abc123                      # 커밋 상세 + diff
git show abc123:src/app.js           # 특정 커밋의 특정 파일 내용
```

### 되돌리기 ◑

Git에서 되돌리기는 상황에 따라 다른 명령어를 사용한다.

```bash
# Working Directory 변경 취소 (스테이징 안 된 변경)
git checkout -- file.js              # 구버전
git restore file.js                  # Git 2.23+ 권장

# 스테이징 취소 (Working Directory 유지)
git reset HEAD file.js               # 구버전
git restore --staged file.js         # Git 2.23+ 권장

# 커밋 취소
git reset --soft HEAD~1              # 커밋 취소, 변경사항 스테이징 상태 유지
git reset --mixed HEAD~1             # 커밋 취소, 변경사항 언스테이징 (기본값)
git reset --hard HEAD~1              # 커밋 취소, 변경사항 완전 삭제 (주의!)

# 특정 커밋으로 되돌리기 (새 커밋 생성)
git revert abc123                    # abc123 커밋의 변경을 되돌리는 새 커밋
git revert abc123..def456            # 범위의 커밋들 되돌리기
git revert --no-commit abc123        # 커밋하지 않고 변경만 적용
```

### ⭕ reset과 revert의 차이는? ◑

이 차이를 이해하는 것이 매우 중요하다.

```
[reset: 이력 재작성]
Before:  A ─ B ─ C ─ D (HEAD)
                      │
After:   A ─ B ─ C (HEAD)    # D 커밋이 사라짐
                │
                └─ D          # (고아 커밋, 나중에 GC됨)

[revert: 이력 보존]
Before:  A ─ B ─ C ─ D (HEAD)

After:   A ─ B ─ C ─ D ─ D' (HEAD)
                          │
                       "Revert D"  # D의 변경을 되돌리는 새 커밋
```

| 명령 | 동작 | 이력 | 사용 시점 |
|------|------|------|----------|
| **reset** | HEAD를 이전 커밋으로 이동, 이후 커밋 제거 | 이력 변경 (재작성) | 아직 push 안 한 로컬 커밋 |
| **revert** | 특정 커밋의 변경을 되돌리는 새 커밋 생성 | 이력 보존 | 이미 push된 공유 브랜치 |

중요: 이미 원격에 push한 커밋을 reset으로 제거하면 다른 팀원들과 이력 충돌이 발생한다. 공유된 브랜치에서는 반드시 revert를 사용해야 한다.

### reset 옵션 상세 ◑

```
상태: Working Directory에 변경, Staging에 추가, Commit 완료

git reset --soft HEAD~1
├─ Working Directory: 유지 (변경된 상태)
├─ Staging Area: 유지 (추가된 상태)
└─ Repository: 커밋 취소

git reset --mixed HEAD~1 (기본값)
├─ Working Directory: 유지 (변경된 상태)
├─ Staging Area: 초기화 (unstaged)
└─ Repository: 커밋 취소

git reset --hard HEAD~1
├─ Working Directory: 초기화 (변경 삭제!)
├─ Staging Area: 초기화
└─ Repository: 커밋 취소
```

---

## 4. 브랜치 ◑

브랜치는 Git의 가장 강력한 기능 중 하나다. 브랜치가 단순한 포인터이기 때문에 생성과 전환이 거의 즉각적이다.

### 브랜치 기본 명령어 ◑

```bash
# 브랜치 목록
git branch                           # 로컬 브랜치
git branch -r                        # 원격 브랜치
git branch -a                        # 모든 브랜치
git branch -v                        # 마지막 커밋과 함께

# 브랜치 생성
git branch feature                   # 생성만
git checkout -b feature              # 생성 + 전환
git switch -c feature                # Git 2.23+ 생성 + 전환

# 브랜치 전환
git checkout feature                 # 구버전
git switch feature                   # Git 2.23+ 권장

# 브랜치 삭제
git branch -d feature                # 병합된 브랜치만 삭제
git branch -D feature                # 강제 삭제 (미병합도)

# 브랜치 이름 변경
git branch -m old-name new-name
git branch -m new-name               # 현재 브랜치 이름 변경

# 원격 브랜치 추적
git checkout -b feature origin/feature  # 원격 브랜치 기반 로컬 생성
git branch -u origin/feature         # 현재 브랜치의 upstream 설정
```

### 병합 (Merge) ◑

```bash
# 기본 병합
git checkout main
git merge feature                    # feature를 main에 병합

# Fast-forward 방지 (병합 커밋 항상 생성)
git merge --no-ff feature

# Squash 병합 (모든 커밋을 하나로 합쳐서 병합)
git merge --squash feature
git commit -m "Merge feature"

# 병합 중단
git merge --abort
```

### ⭕ Fast-forward vs 3-way Merge ◑

병합 방식은 브랜치의 분기 상태에 따라 달라진다.

```
[Fast-forward Merge]
조건: main에서 분기 후 main에 추가 커밋이 없음

Before:
main:     A ─ B (main)
               ↘
feature:        C ─ D (feature)

After (git merge feature):
main:     A ─ B ─ C ─ D (main, feature)

→ main 포인터가 앞으로 이동할 뿐, 새 커밋이 생기지 않음


[3-way Merge]
조건: main과 feature 양쪽에 새 커밋이 있음

Before:
main:     A ─ B ─ E (main)
               ↘
feature:        C ─ D (feature)

After (git merge feature):
main:     A ─ B ─ E ─ M (main)
               ↘     ↗
feature:        C ─ D (feature)

→ 공통 조상(B), main(E), feature(D)를 비교하여 병합 커밋(M) 생성
```

| 방식 | 조건 | 특징 | 이력 |
|------|------|------|------|
| **Fast-forward** | base 브랜치에 추가 커밋 없음 | 병합 커밋 없음 | 선형, 깔끔 |
| **3-way Merge** | 양쪽 모두 새 커밋 있음 | 병합 커밋 생성 | 분기 이력 보존 |

`--no-ff` 옵션: Fast-forward가 가능한 상황에서도 병합 커밋을 강제로 생성한다. 기능 브랜치의 병합 시점을 명확히 기록하고 싶을 때 유용하다.

### Rebase ◑

Rebase는 커밋들을 다른 베이스로 재배치(re-base)하는 작업이다.

```bash
# 기본 리베이스
git checkout feature
git rebase main                      # feature의 커밋들을 main 위로 재배치

# 인터랙티브 리베이스 (커밋 정리)
git rebase -i HEAD~3                 # 최근 3개 커밋 편집
```

인터랙티브 리베이스 옵션:

| 명령 | 설명 |
|------|------|
| `pick` | 커밋 사용 (그대로 유지) |
| `reword` | 커밋 메시지 수정 |
| `edit` | 커밋 내용 수정 (멈춤) |
| `squash` | 이전 커밋에 합침 (메시지 합침) |
| `fixup` | 이전 커밋에 합침 (메시지 버림) |
| `drop` | 커밋 삭제 |

```
# 인터랙티브 리베이스 예시
pick abc123 Add feature A
squash def456 Fix typo in feature A
squash 789ghi Add more to feature A
pick jkl012 Add feature B

→ 첫 세 커밋이 하나로 합쳐짐
```

### ⭕ Merge vs Rebase ◑

```
[Merge 결과]
main:     A ─ B ─ C ────────── M (main)
               ↘              ↗
feature:        D ─ E ─ F ───┘ (feature)

이력: 분기와 병합 시점이 명확히 보임


[Rebase 결과]
main:     A ─ B ─ C (main)
                   ↘
feature:            D' ─ E' ─ F' (feature)

이력: feature의 커밋들이 main 뒤에 선형으로 배치
     (D, E, F는 새로운 커밋 D', E', F'로 재작성됨)
```

| 방식 | 장점 | 단점 | 사용 시점 |
|------|------|------|----------|
| **Merge** | 이력 완전 보존, 안전함, 충돌 한 번만 해결 | 복잡한 브랜치 그래프 | 공유 브랜치, 팀 협업 |
| **Rebase** | 깔끔한 선형 이력, 쉬운 이력 탐색 | 이력 재작성, 각 커밋마다 충돌 해결 | 로컬/개인 브랜치 |

황금 규칙: **이미 공유된(push된) 브랜치는 절대 rebase하지 않는다.** Rebase는 커밋의 해시가 바뀌므로, 다른 사람의 작업과 충돌을 일으킨다.

```bash
# 올바른 rebase 워크플로우
git checkout feature
git fetch origin
git rebase origin/main               # 로컬 feature를 원격 main 위로 재배치
git push -f origin feature           # 개인 브랜치만 force push 가능
```

---

## 5. 원격 저장소 ◑

### 원격 관련 명령어 ◑

```bash
# 원격 저장소 확인
git remote -v                        # URL과 함께 목록 표시
git remote show origin               # 원격 상세 정보

# 원격 추가/수정/삭제
git remote add origin <url>          # 원격 추가
git remote set-url origin <new-url>  # URL 변경
git remote rename origin upstream    # 이름 변경
git remote remove origin             # 삭제

# 원격에서 가져오기
git fetch origin                     # 모든 브랜치의 변경사항 다운로드 (병합 X)
git fetch origin main                # 특정 브랜치만
git fetch --all                      # 모든 원격의 모든 브랜치
git fetch --prune                    # 삭제된 원격 브랜치 정리

# 원격에서 가져와서 병합
git pull origin main                 # fetch + merge
git pull --rebase origin main        # fetch + rebase
git pull --ff-only                   # fast-forward만 허용

# 원격으로 보내기
git push origin main                 # 특정 브랜치 푸시
git push -u origin main              # upstream 설정 + 푸시
git push --all origin                # 모든 브랜치 푸시
git push --tags                      # 태그만 푸시
git push --force                     # 강제 푸시 (위험!)
git push --force-with-lease          # 안전한 강제 푸시 (권장)
```

### ⭕ fetch와 pull의 차이는? ◑

```
[git fetch]
원격 저장소                    로컬 저장소
┌────────────┐               ┌────────────────────────┐
│ origin/main│               │ origin/main (updated)  │
│    A─B─C   │ ──────────→   │      A─B─C             │
└────────────┘    fetch      │                        │
                             │ main (변경 없음)        │
                             │      A─B               │
                             └────────────────────────┘

→ 원격 변경사항만 다운로드, 로컬 브랜치는 그대로


[git pull = fetch + merge]
원격 저장소                    로컬 저장소
┌────────────┐               ┌────────────────────────┐
│ origin/main│               │ origin/main (updated)  │
│    A─B─C   │ ──────────→   │      A─B─C             │
└────────────┘    pull       │                        │
                             │ main (병합됨)           │
                             │      A─B─C             │
                             └────────────────────────┘

→ 원격 변경사항 다운로드 + 로컬 브랜치에 자동 병합
```

| 명령 | 동작 | 로컬 작업 영향 |
|------|------|--------------|
| **fetch** | 원격 변경사항만 다운로드 | 없음 (안전) |
| **pull** | fetch + merge (또는 rebase) | 자동 병합 |

fetch를 선호하는 이유: 원격 변경사항을 먼저 확인한 후 병합 여부를 결정할 수 있다.

```bash
git fetch origin
git log origin/main ^main            # 원격에만 있는 커밋 확인
git diff main origin/main            # 차이점 확인
git merge origin/main                # 확인 후 병합
```

### force push 주의사항 ◑

```bash
# 위험: 원격 이력 덮어쓰기
git push --force origin main         # 절대 main에 사용 금지!

# 안전한 대안: 다른 사람이 push했으면 실패
git push --force-with-lease origin feature
```

`--force-with-lease`: 내가 마지막으로 fetch한 이후로 원격에 새 커밋이 없을 때만 force push를 허용한다. 실수로 다른 사람의 작업을 덮어쓰는 것을 방지한다.

---

## 6. 충돌 해결 ◑

### 충돌이 발생하는 경우 ◑

Git은 대부분의 병합을 자동으로 처리하지만, 같은 파일의 같은 부분이 다르게 수정되면 충돌이 발생한다.

```
[충돌 발생 시나리오]
원본 (공통 조상):
function greet() {
  return "Hello";
}

브랜치 A:                        브랜치 B:
function greet() {               function greet() {
  return "Hello, World!";          return "Hi!";
}                                }

→ Git이 어느 쪽을 선택해야 할지 모름 → 충돌!
```

### 충돌 표시 형식 ◑

충돌이 발생하면 Git은 파일에 충돌 마커를 삽입한다.

```javascript
function greet() {
<<<<<<< HEAD
  return "Hello, World!";        // 현재 브랜치 (main)의 내용
=======
  return "Hi!";                  // 병합하려는 브랜치 (feature)의 내용
>>>>>>> feature
}
```

| 마커 | 의미 |
|------|------|
| `<<<<<<< HEAD` | 현재 브랜치의 변경 시작 |
| `=======` | 구분선 |
| `>>>>>>> branch` | 병합 브랜치의 변경 끝 |

### 충돌 해결 과정 ◑

```bash
# 1. 충돌 발생
git merge feature
# Auto-merging app.js
# CONFLICT (content): Merge conflict in app.js
# Automatic merge failed; fix conflicts and then commit the result.

# 2. 충돌 파일 확인
git status
# both modified: app.js

# 3. 파일을 열어 충돌 해결
#    - 충돌 마커 제거
#    - 원하는 내용으로 수정

# 4. 해결된 파일 스테이징
git add app.js

# 5. 병합 커밋 완료
git commit
# (자동으로 "Merge branch 'feature'" 메시지가 입력됨)
```

### 충돌 해결 도구 ◑

```bash
# 3-way 병합 도구 사용
git mergetool

# 충돌 해결 시 특정 쪽 선택
git checkout --ours file.js      # 현재 브랜치 내용 사용
git checkout --theirs file.js    # 병합 브랜치 내용 사용

# 병합 중단
git merge --abort
```

### 충돌 방지 전략 ◑

1. **자주 통합**: 작은 단위로 자주 병합하여 충돌 범위 최소화
2. **같은 파일 동시 수정 피하기**: 작업 범위 사전 조율
3. **최신 상태 유지**: 작업 전 `git pull`로 최신화
4. **작은 커밋**: 논리적 단위로 작은 커밋을 만들어 충돌 해결 용이하게

---

## 7. Git 워크플로우 ◑

팀이나 프로젝트의 특성에 따라 다양한 브랜치 전략을 사용할 수 있다.

### ⭕ Git Flow ◑

복잡한 릴리스 주기를 가진 프로젝트에 적합한 워크플로우다.

```
         hotfix                    release
           │                          │
           ▼                          ▼
main ──●───●───────────────────●──────●──────●──────→  (프로덕션)
       │   ↑                   ↑      │      ↑
       │   │                   │      │      │
       │   │                   │      ↓      │
       │   └───────────────────┼──────●──────┤
       │                       │             │
       ▼                       │             │
develop ●──●──●──●──●──●──●───●──●──●──●───●──→  (개발 통합)
         ↑  │     ↑  │           ↑
         │  │     │  │           │
         │  ▼     │  ▼           │
       feature  feature      feature
```

| 브랜치 | 역할 | 생성 | 병합 |
|--------|------|------|------|
| **main** | 배포된 프로덕션 코드 | - | release, hotfix에서 |
| **develop** | 개발 통합 브랜치 | - | feature에서 |
| **feature/** | 기능 개발 | develop에서 | develop으로 |
| **release/** | 배포 준비 (QA, 버전업) | develop에서 | main, develop으로 |
| **hotfix/** | 프로덕션 긴급 버그 수정 | main에서 | main, develop으로 |

```bash
# Git Flow 사용 예시
# 기능 개발 시작
git checkout develop
git checkout -b feature/login

# 기능 완료
git checkout develop
git merge --no-ff feature/login
git branch -d feature/login

# 릴리스 준비
git checkout -b release/1.0.0 develop
# ... QA, 버그 수정 ...
git checkout main
git merge --no-ff release/1.0.0
git tag -a v1.0.0
git checkout develop
git merge --no-ff release/1.0.0

# 긴급 수정
git checkout -b hotfix/critical-bug main
# ... 버그 수정 ...
git checkout main
git merge --no-ff hotfix/critical-bug
git tag -a v1.0.1
git checkout develop
git merge --no-ff hotfix/critical-bug
```

**장점**: 체계적인 버전 관리, 명확한 브랜치 역할
**단점**: 복잡함, 빠른 배포에 부적합
**적합**: 명시적인 버전 릴리스가 필요한 프로젝트 (모바일 앱, 패키지 라이브러리)

### GitHub Flow ◑

단순하고 지속적 배포에 적합한 워크플로우다.

```
main ──●──────●──────────●──────────●──────→ (항상 배포 가능)
       │      ↑          ↑          ↑
       │      │          │          │
       ↓      │          │          │
     feature──●          │          │
              ↑          │          │
              PR         │          │
              Review     │          │
              Merge      │          │
                         │          │
                    feature──●      │
                              ↑     │
                              PR    │
                              ...   │
```

| 원칙 | 설명 |
|------|------|
| main은 항상 배포 가능 | 테스트 통과, 즉시 배포 가능 상태 |
| 기능은 브랜치에서 개발 | main에서 분기, 의미 있는 이름 |
| PR로 리뷰 | 코드 리뷰 후 병합 |
| 병합 = 배포 | CI/CD로 자동 배포 |

```bash
# GitHub Flow 예시
# 1. 브랜치 생성
git checkout main
git pull
git checkout -b feature/add-user-profile

# 2. 개발 및 커밋
git add .
git commit -m "Add user profile page"
git push -u origin feature/add-user-profile

# 3. PR 생성 (GitHub에서)
# 4. 코드 리뷰
# 5. CI 통과 확인
# 6. Merge (GitHub에서)
# 7. 자동 배포 (CI/CD)
```

**장점**: 단순함, CI/CD에 최적, 빠른 피드백
**단점**: 복잡한 릴리스 주기 지원 어려움
**적합**: 웹 서비스, SaaS, 지속적 배포 환경

### Trunk-Based Development ◑

메인 브랜치에 자주 직접 통합하는 방식이다.

```
main (trunk) ──●──●──●──●──●──●──●──●──●──→
               ↑  ↑     ↑           ↑
               │  │     │           │
         short-lived branches (1-2일)
```

| 특징 | 설명 |
|------|------|
| 짧은 수명의 브랜치 | 최대 1-2일, 작은 변경 |
| 자주 통합 | 하루에 여러 번 main에 병합 |
| Feature Flag | 미완성 기능은 플래그로 숨김 |
| 높은 테스트 커버리지 | 자동 테스트 필수 |

```javascript
// Feature Flag 예시
if (featureFlags.newUserProfile) {
  // 새 기능 코드 (아직 미완성)
  return <NewUserProfile />;
} else {
  // 기존 코드
  return <UserProfile />;
}
```

**장점**: 통합 지옥 방지, 빠른 피드백, 간단한 브랜치 관리
**단점**: Feature Flag 관리 복잡, 높은 테스트/CI 요구
**적합**: 숙련된 팀, 마이크로서비스, 빠른 반복이 필요한 환경

### 워크플로우 선택 가이드 ◑

| 워크플로우 | 릴리스 주기 | 팀 규모 | 복잡도 |
|------------|-------------|---------|--------|
| **Git Flow** | 정기적, 버전 기반 | 대규모 | 높음 |
| **GitHub Flow** | 지속적 | 소-중규모 | 낮음 |
| **Trunk-Based** | 매우 빈번 | 숙련된 팀 | 중간 (인프라 의존) |

---

## 8. 고급 기능 ◑

### Stash ◑

현재 작업 중인 변경사항을 임시로 저장하고 깨끗한 상태로 돌아간다.

```bash
# 기본 사용
git stash                            # Working + Staging 저장
git stash push -m "설명 메시지"       # 메시지와 함께
git stash -u                         # Untracked 파일도 포함
git stash -a                         # .gitignore 파일도 포함

# 스태시 목록
git stash list
# stash@{0}: WIP on main: abc123 Last commit message
# stash@{1}: On main: 설명 메시지

# 복원
git stash pop                        # 최신 스태시 복원 + 삭제
git stash pop stash@{1}              # 특정 스태시 복원
git stash apply                      # 복원 (삭제 안 함)

# 관리
git stash drop stash@{0}             # 특정 스태시 삭제
git stash clear                      # 모든 스태시 삭제
git stash show -p stash@{0}          # 스태시 내용 확인
```

사용 시나리오: 작업 중에 급히 다른 브랜치에서 버그를 수정해야 할 때

```bash
# 1. 현재 작업 저장
git stash push -m "feature 작업 중"

# 2. 브랜치 전환하여 버그 수정
git checkout main
git checkout -b hotfix/urgent-bug
# ... 버그 수정 ...
git commit -m "Fix urgent bug"
git checkout main
git merge hotfix/urgent-bug

# 3. 원래 작업으로 복귀
git checkout feature
git stash pop
```

### Cherry-pick ◑

다른 브랜치의 특정 커밋만 현재 브랜치에 적용한다.

```bash
git cherry-pick abc123               # 특정 커밋 적용
git cherry-pick abc123 def456        # 여러 커밋
git cherry-pick abc123..def456       # 범위 (abc123 제외, def456 포함)
git cherry-pick -n abc123            # 커밋하지 않고 변경만 적용
git cherry-pick --abort              # 중단
git cherry-pick --continue           # 충돌 해결 후 계속
```

사용 시나리오:

```
[특정 버그 수정을 다른 브랜치에도 적용]

develop:  A ─ B ─ C ─ D (bugfix는 C)
main:     A ─ B ─ X

git checkout main
git cherry-pick C

main:     A ─ B ─ X ─ C' (C와 같은 내용의 새 커밋)
```

### Bisect ◑

이진 탐색으로 버그가 도입된 커밋을 찾는다. 수백 개의 커밋에서도 빠르게 원인을 찾을 수 있다.

```bash
# 1. bisect 시작
git bisect start

# 2. 현재(버그 있음)를 bad로 표시
git bisect bad

# 3. 정상 작동하던 커밋을 good으로 표시
git bisect good abc123

# 4. Git이 중간 커밋으로 체크아웃
# Bisecting: 64 revisions left to test after this (roughly 6 steps)
# [커밋 해시] 커밋 메시지

# 5. 테스트 후 good 또는 bad 표시
git bisect good    # 이 커밋은 정상
# 또는
git bisect bad     # 이 커밋에도 버그 있음

# 6. 반복... Git이 범위를 절반씩 좁혀감

# 7. 원인 커밋 발견!
# abc123def is the first bad commit

# 8. bisect 종료
git bisect reset
```

자동화:

```bash
# 테스트 스크립트로 자동화
git bisect start HEAD abc123
git bisect run ./test-script.sh      # 스크립트가 0이면 good, 아니면 bad
```

### Git Reflog ◑

HEAD의 모든 이동 이력을 기록한다. 실수로 삭제한 커밋을 복구할 때 유용하다.

```bash
git reflog
# abc123 HEAD@{0}: commit: Add feature
# def456 HEAD@{1}: checkout: moving from main to feature
# 789ghi HEAD@{2}: reset: moving to HEAD~1
# jkl012 HEAD@{3}: commit: Deleted commit (복구 가능!)

# 삭제된 커밋 복구
git checkout jkl012                  # 해당 커밋으로 이동
git branch recovered jkl012          # 브랜치 생성
```

### Git Hooks ◑

특정 Git 이벤트 시 자동으로 스크립트를 실행한다.

```
.git/hooks/
├── pre-commit        # 커밋 전 (린트, 포맷팅)
├── commit-msg        # 커밋 메시지 검증
├── pre-push          # 푸시 전 (테스트)
├── post-merge        # 병합 후 (의존성 설치)
└── ...
```

예시 (pre-commit):

```bash
#!/bin/sh
# .git/hooks/pre-commit

# 린트 검사
npm run lint
if [ $? -ne 0 ]; then
  echo "Lint 오류! 커밋 중단."
  exit 1
fi

# 테스트 실행
npm test
if [ $? -ne 0 ]; then
  echo "테스트 실패! 커밋 중단."
  exit 1
fi
```

실무에서는 Husky, lefthook 같은 도구로 훅을 관리한다:

```json
// package.json
{
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged",
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
    }
  }
}
```

### Submodule ◑

저장소 안에 다른 저장소를 포함한다. 공통 라이브러리를 여러 프로젝트에서 공유할 때 유용하다.

```bash
# 서브모듈 추가
git submodule add https://github.com/user/lib.git libs/lib

# 클론 시 서브모듈 포함
git clone --recurse-submodules https://github.com/user/main.git

# 기존 저장소에서 서브모듈 초기화
git submodule update --init --recursive

# 서브모듈 업데이트
cd libs/lib
git checkout main
git pull
cd ../..
git add libs/lib
git commit -m "Update submodule"
```

---

## 9. 좋은 커밋 작성법 ◑

### 커밋 메시지 컨벤션 ◑

잘 작성된 커밋 메시지는 프로젝트 이력을 이해하기 쉽게 만든다.

```
<type>(<scope>): <subject>

<body>

<footer>
```

예시:

```
feat(auth): 소셜 로그인 기능 추가

Google, GitHub OAuth 연동 구현
- Google OAuth 2.0 인증 플로우
- GitHub OAuth 앱 연동
- 사용자 프로필 자동 생성

Closes #123
```

### Conventional Commits ◑

널리 사용되는 커밋 메시지 규약이다.

| 타입 | 설명 | 예시 |
|------|------|------|
| **feat** | 새 기능 | `feat: 사용자 프로필 페이지 추가` |
| **fix** | 버그 수정 | `fix: 로그인 실패 시 에러 처리` |
| **docs** | 문서 변경 | `docs: README 설치 가이드 업데이트` |
| **style** | 포맷팅, 세미콜론 등 (코드 로직 변경 없음) | `style: ESLint 경고 수정` |
| **refactor** | 리팩토링 (기능 변경 없음) | `refactor: 인증 로직 분리` |
| **perf** | 성능 개선 | `perf: 이미지 지연 로딩 적용` |
| **test** | 테스트 추가/수정 | `test: 로그인 단위 테스트 추가` |
| **chore** | 빌드, 설정, 의존성 | `chore: 패키지 버전 업데이트` |
| **ci** | CI 설정 | `ci: GitHub Actions 워크플로우 추가` |

### 좋은 커밋의 7가지 규칙 ◑

1. **제목과 본문을 빈 줄로 분리**
2. **제목은 50자 이내**
3. **제목은 대문자로 시작** (영문의 경우)
4. **제목 끝에 마침표 없음**
5. **제목은 명령형으로** ("Add feature" not "Added feature")
6. **본문은 72자에서 줄바꿈**
7. **본문에는 무엇을, 왜를 설명** (어떻게는 코드가 설명)

### 원자적 커밋 (Atomic Commit) ◑

하나의 커밋은 하나의 논리적 변경만 포함해야 한다.

```
# 나쁜 예
git commit -m "Fix bug, add feature, update docs"

# 좋은 예
git commit -m "fix: 로그인 세션 만료 버그 수정"
git commit -m "feat: 비밀번호 재설정 기능 추가"
git commit -m "docs: 인증 API 문서 업데이트"
```

원자적 커밋의 장점:
- 특정 변경만 되돌리기 쉬움 (revert)
- 코드 리뷰가 용이함
- 버그 원인 추적이 쉬움 (bisect)
- 이력 이해가 쉬움

---

## 10. .gitignore ◑

### 문법 ◑

```gitignore
# 주석

# 특정 파일
secret.txt

# 특정 확장자
*.log
*.tmp

# 디렉터리
node_modules/
build/

# 루트의 특정 파일/디렉터리만
/config.local.js
/dist/

# 패턴
*.min.js           # 모든 .min.js
**/temp/           # 모든 위치의 temp 디렉터리
doc/**/*.pdf       # doc 하위의 모든 pdf

# 예외 (느낌표)
*.log
!important.log     # important.log는 추적

# 빈 디렉터리 (Git은 빈 디렉터리 추적 안 함)
# 빈 디렉터리를 유지하려면 .gitkeep 파일 추가
```

### 일반적인 .gitignore 패턴 ◑

```gitignore
# ===== 의존성 =====
node_modules/
vendor/
.venv/
__pycache__/

# ===== 빌드 결과물 =====
build/
dist/
out/
*.exe
*.dll
*.so
*.class
*.pyc

# ===== 환경 설정 (민감 정보) =====
.env
.env.local
.env.*.local
*.local
secrets.json

# ===== IDE/에디터 =====
.idea/
.vscode/
*.swp
*.swo
*~
.DS_Store

# ===== 로그/임시 파일 =====
*.log
npm-debug.log*
logs/
tmp/
temp/

# ===== 테스트/커버리지 =====
coverage/
.nyc_output/
*.lcov

# ===== OS 생성 파일 =====
.DS_Store
Thumbs.db
desktop.ini
```

### 이미 추적 중인 파일 무시 ◑

`.gitignore`에 추가해도 이미 추적 중인 파일은 계속 추적된다.

```bash
# 파일 추적 중단 (파일은 유지)
git rm --cached file.txt

# 디렉터리 추적 중단
git rm -r --cached node_modules/

# .gitignore 변경 후 전체 캐시 갱신
git rm -r --cached .
git add .
git commit -m "chore: .gitignore 적용"
```

---

## 11. Git 설정 ◑

### 기본 설정 ◑

```bash
# 사용자 정보 (필수)
git config --global user.name "Your Name"
git config --global user.email "your@email.com"

# 기본 에디터
git config --global core.editor "code --wait"  # VS Code
git config --global core.editor "vim"          # Vim

# 기본 브랜치 이름
git config --global init.defaultBranch main

# 줄바꿈 처리
git config --global core.autocrlf true   # Windows
git config --global core.autocrlf input  # Mac/Linux

# pull 전략
git config --global pull.rebase true     # pull 시 rebase 사용
git config --global pull.ff only         # fast-forward만 허용
```

### 유용한 Alias ◑

```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.lg "log --oneline --graph --all"
git config --global alias.unstage "reset HEAD --"
git config --global alias.last "log -1 HEAD"
git config --global alias.amend "commit --amend --no-edit"
```

### 설정 확인 ◑

```bash
git config --list                        # 모든 설정
git config --global --list               # 전역 설정
git config user.name                     # 특정 설정

# 설정 파일 위치
# 전역: ~/.gitconfig 또는 ~/.config/git/config
# 로컬: .git/config
```

---

## 면접 대비 체크리스트 ◑

- [ ] Git의 세 가지 영역 (Working Directory, Staging Area, Repository)
- [ ] Git 객체 모델 (Blob, Tree, Commit, Tag)
- [ ] reset vs revert 차이와 사용 시점
- [ ] reset의 세 가지 모드 (--soft, --mixed, --hard)
- [ ] Fast-forward vs 3-way Merge
- [ ] Merge vs Rebase 차이와 사용 시점
- [ ] 왜 공유된 브랜치에서 rebase를 하면 안 되는가
- [ ] fetch vs pull 차이
- [ ] Git Flow vs GitHub Flow vs Trunk-Based Development
- [ ] 충돌 해결 과정
- [ ] Stash, Cherry-pick, Bisect 사용법과 활용 시나리오
- [ ] 좋은 커밋 메시지 작성법
- [ ] .gitignore 문법과 이미 추적 중인 파일 제외 방법
- [ ] force push의 위험성과 --force-with-lease
