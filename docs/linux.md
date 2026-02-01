# 18. Linux/Unix 기초 (Linux/Unix Fundamentals) ◑

---

## 1. Linux 개요 ◑

### Linux란?

Unix 계열의 오픈소스 운영체제로, 서버, 클라우드, 임베디드 시스템에서 널리 사용된다. 1991년 리누스 토르발스(Linus Torvalds)가 개발한 커널을 기반으로 한다.

```
Linux 시스템 구조:

┌────────────────────────────────────────────────────┐
│                    사용자 애플리케이션              │
├────────────────────────────────────────────────────┤
│              셸 (bash, zsh, fish)                  │
├────────────────────────────────────────────────────┤
│         시스템 라이브러리 (glibc, libc)             │
├────────────────────────────────────────────────────┤
│   시스템 콜 인터페이스 (read, write, fork, exec)   │
├────────────────────────────────────────────────────┤
│               Linux 커널                           │
│  ┌──────────┬──────────┬──────────┬──────────┐    │
│  │프로세스   │메모리     │파일       │네트워크   │    │
│  │관리      │관리       │시스템     │스택      │    │
│  └──────────┴──────────┴──────────┴──────────┘    │
├────────────────────────────────────────────────────┤
│                   하드웨어                         │
└────────────────────────────────────────────────────┘
```

### Unix 철학 ◑

Unix/Linux의 설계 원칙은 간결함과 조합 가능성을 강조한다.

1. **한 가지 일을 잘 하라**: 각 프로그램은 하나의 기능에 집중
2. **프로그램을 연결하라**: 작은 프로그램들을 파이프로 조합
3. **텍스트 스트림을 사용하라**: 텍스트는 보편적 인터페이스
4. **모든 것은 파일이다**: 장치, 프로세스, 네트워크도 파일로 추상화

```bash
# Unix 철학의 예: 작은 도구들의 조합
cat access.log | grep "404" | awk '{print $1}' | sort | uniq -c | sort -rn | head -10
```

이 한 줄 명령은 로그에서 404 에러를 발생시킨 IP 주소를 빈도순으로 정렬하여 상위 10개를 보여준다. 각 명령어는 단순하지만, 조합하면 복잡한 작업을 수행할 수 있다.

### Unix vs Linux ◑

| 구분 | Unix | Linux |
|------|------|-------|
| 라이선스 | 상용 (AT&T, IBM, Sun) | 오픈소스 (GPL) |
| 커널 | 벤더별 상이 | Linux 커널 |
| 예시 | Solaris, AIX, HP-UX | Ubuntu, CentOS, Debian |
| 철학 | 원본 | Unix 철학 계승 |
| POSIX | 준수 | 대부분 준수 |

### 주요 배포판 ◑

| 계열 | 배포판 | 특징 | 패키지 관리 |
|------|--------|------|------------|
| **Debian** | Ubuntu, Debian | 사용자 친화적, 커뮤니티 활발 | apt |
| **Red Hat** | RHEL, CentOS, Fedora | 엔터프라이즈 안정성 | yum/dnf |
| **Arch** | Arch Linux, Manjaro | 롤링 릴리즈, 최신 패키지 | pacman |
| **Alpine** | Alpine Linux | 경량 (5MB), 컨테이너용 | apk |
| **SUSE** | openSUSE, SLES | 엔터프라이즈, YaST 관리도구 | zypper |

```
배포판 선택 가이드:

데스크톱 사용자 ───▶ Ubuntu, Linux Mint
서버 운영 ─────────▶ Ubuntu LTS, RHEL/CentOS, Debian
컨테이너 이미지 ───▶ Alpine Linux
최신 기술 시험 ────▶ Fedora, Arch Linux
엔터프라이즈 ──────▶ RHEL, SUSE Linux Enterprise
```

---

## 2. 파일 시스템 구조 ◑

### ⭕ 디렉터리 계층 (FHS) ◑

FHS(Filesystem Hierarchy Standard)는 Linux 시스템의 표준 디렉터리 구조를 정의한다.

```
/
├── bin/      # 필수 사용자 명령어 (ls, cp, mv)
├── sbin/     # 시스템 관리 명령어 (fdisk, iptables)
├── etc/      # 시스템 설정 파일
├── home/     # 사용자 홈 디렉터리
├── root/     # root 사용자 홈
├── var/      # 가변 데이터 (로그, 캐시, 메일)
│   ├── log/  # 시스템 로그
│   ├── cache/
│   └── spool/
├── tmp/      # 임시 파일 (재부팅 시 삭제)
├── usr/      # 사용자 프로그램 (User System Resources)
│   ├── bin/      # 대부분의 사용자 명령어
│   ├── lib/      # 라이브러리
│   ├── share/    # 아키텍처 독립 데이터
│   └── local/    # 로컬 설치 소프트웨어
├── opt/      # 추가/서드파티 소프트웨어
├── lib/      # 필수 공유 라이브러리
├── lib64/    # 64비트 라이브러리
├── dev/      # 장치 파일
├── proc/     # 프로세스 정보 (가상 파일시스템)
├── sys/      # 시스템/커널 정보 (가상)
├── run/      # 런타임 데이터 (PID 파일 등)
├── mnt/      # 임시 마운트 포인트
├── media/    # 이동식 미디어 마운트
├── srv/      # 서비스 데이터 (웹, FTP)
└── boot/     # 부트로더, 커널 이미지
```

### 주요 디렉터리 상세 ◑

| 디렉터리 | 용도 | 예시 파일 |
|----------|------|-----------|
| **/etc** | 시스템 설정 | passwd, hosts, nginx/nginx.conf |
| **/var/log** | 로그 파일 | syslog, auth.log, nginx/access.log |
| **/home/user** | 사용자 데이터 | .bashrc, .ssh/, Documents/ |
| **/tmp** | 임시 파일 | 재부팅 시 삭제됨 |
| **/proc** | 프로세스/커널 정보 | cpuinfo, meminfo, /proc/[pid]/ |
| **/dev** | 장치 파일 | sda, tty, null, random |

### 특수 장치 파일 ◑

```bash
/dev/null   # 블랙홀 (출력 버리기)
/dev/zero   # 0 바이트 생성
/dev/random # 랜덤 바이트 (블로킹)
/dev/urandom # 랜덤 바이트 (논블로킹)
/dev/sda    # 첫 번째 SATA/SCSI 디스크
/dev/sda1   # 첫 번째 디스크의 첫 번째 파티션
/dev/tty    # 현재 터미널
```

```bash
# /dev/null 활용: 출력 버리기
command > /dev/null 2>&1

# /dev/zero 활용: 1GB 파일 생성
dd if=/dev/zero of=testfile bs=1M count=1024
```

### /proc 가상 파일시스템 ◑

/proc은 커널이 제공하는 가상 파일시스템으로, 프로세스와 시스템 정보를 파일처럼 읽을 수 있다.

```bash
# 시스템 정보
cat /proc/cpuinfo      # CPU 정보
cat /proc/meminfo      # 메모리 정보
cat /proc/version      # 커널 버전
cat /proc/loadavg      # 시스템 부하

# 프로세스 정보 (/proc/[PID]/)
cat /proc/1234/cmdline   # 실행 명령
cat /proc/1234/status    # 상태 정보
cat /proc/1234/fd/       # 열린 파일 디스크립터
cat /proc/1234/environ   # 환경 변수
```

---

## 3. 기본 명령어 ◑

### 파일/디렉터리 조작

```bash
# 이동/확인
pwd                     # 현재 디렉터리
cd /path/to/dir         # 디렉터리 이동
cd ~                    # 홈 디렉터리로
cd -                    # 이전 디렉터리로
ls -la                  # 목록 (상세, 숨김 포함)
ls -lh                  # 사람이 읽기 쉬운 크기
ls -lt                  # 수정 시간순 정렬
ls -lS                  # 크기순 정렬

# 생성/삭제
mkdir -p dir/subdir     # 디렉터리 생성 (-p: 부모 포함)
touch file.txt          # 빈 파일 생성 / 타임스탬프 갱신
rm file.txt             # 파일 삭제
rm -rf dir/             # 디렉터리 강제 삭제 (주의!)
rmdir dir/              # 빈 디렉터리 삭제

# 복사/이동
cp src dst              # 복사
cp -r srcdir dstdir     # 디렉터리 복사
cp -p src dst           # 권한, 타임스탬프 보존
mv src dst              # 이동/이름 변경

# 링크
ln -s /path/to/target linkname   # 심볼릭 링크
ln /path/to/file hardlink        # 하드 링크
```

### ⭕ 하드 링크 vs 심볼릭 링크 ◑

```
하드 링크:
file.txt ──────┬──▶ [inode] ──▶ [데이터 블록]
hardlink.txt ──┘

심볼릭 링크:
symlink.txt ──▶ "file.txt" ──▶ [inode] ──▶ [데이터 블록]
```

| 구분 | 하드 링크 | 심볼릭 링크 |
|------|-----------|-------------|
| 대상 | 같은 inode 참조 | 경로명 저장 |
| 파일 시스템 | 같은 파일시스템만 | 다른 파일시스템 가능 |
| 디렉터리 | 불가 | 가능 |
| 원본 삭제 시 | 데이터 유지 | 깨진 링크 |
| 용량 | 추가 용량 없음 | 경로명만큼 |

### 파일 내용 확인

```bash
cat file.txt            # 전체 출력
less file.txt           # 페이지 단위 (스크롤, 검색)
more file.txt           # 페이지 단위 (전진만)
head -n 20 file.txt     # 처음 20줄
tail -n 20 file.txt     # 마지막 20줄
tail -f file.txt        # 실시간 추적 (로그 모니터링)
tail -F file.txt        # 파일 회전 시에도 추적

# 파일 비교
diff file1 file2        # 차이 비교
diff -u file1 file2     # Unified 형식 (패치용)
comm file1 file2        # 공통/고유 라인 비교

# 파일 정보
file filename           # 파일 타입 확인
stat filename           # 상세 정보 (inode, 크기, 시간)
```

### 검색

```bash
# find: 파일 검색
find /path -name "*.log"           # 이름으로 검색
find /path -type f -mtime -7       # 7일 내 수정된 파일
find /path -type f -size +100M     # 100MB 이상 파일
find /path -name "*.tmp" -delete   # 찾아서 삭제
find /path -type f -exec chmod 644 {} \;   # 찾아서 실행
find /path -type f -perm 777       # 권한으로 검색
find /path -user root              # 소유자로 검색

# grep: 내용 검색
grep "pattern" file.txt            # 패턴 검색
grep -r "pattern" /path            # 재귀 검색
grep -i "pattern" file.txt         # 대소문자 무시
grep -v "pattern" file.txt         # 패턴 제외
grep -n "pattern" file.txt         # 줄 번호 표시
grep -l "pattern" /path/*          # 매칭 파일명만
grep -c "pattern" file.txt         # 매칭 횟수
grep -E "regex" file.txt           # 확장 정규식 (egrep)
grep -A 3 -B 2 "pattern" file      # 앞뒤 줄 포함

# locate: 빠른 검색 (인덱스 기반)
locate filename
updatedb                            # 인덱스 갱신
```

### 텍스트 처리

```bash
wc -l file.txt          # 줄 수
wc -w file.txt          # 단어 수
wc -c file.txt          # 바이트 수

sort file.txt           # 정렬
sort -n file.txt        # 숫자로 정렬
sort -r file.txt        # 역순
sort -k2 file.txt       # 2번째 필드 기준

uniq                    # 중복 제거 (정렬된 입력)
uniq -c                 # 중복 횟수 표시
uniq -d                 # 중복된 것만 표시

cut -d',' -f1 file.csv  # 구분자로 필드 추출
cut -c1-10 file.txt     # 1-10번째 문자

tr 'a-z' 'A-Z'          # 문자 변환 (소문자→대문자)
tr -d '\r'              # 문자 삭제 (Windows 줄바꿈)
```

### awk와 sed ◑

**awk**: 패턴 스캐닝과 텍스트 처리

```bash
# 기본 구조: awk 'pattern { action }' file

awk '{print $1}' file           # 첫 번째 필드
awk '{print $1, $3}' file       # 1, 3번째 필드
awk -F',' '{print $1}' file.csv # CSV 파싱
awk 'NR > 1' file               # 첫 줄 제외
awk '$3 > 100' file             # 3번째 필드가 100 초과
awk '{sum += $1} END {print sum}' file  # 합계

# 실용 예시: 로그에서 IP별 요청 수
awk '{print $1}' access.log | sort | uniq -c | sort -rn
```

**sed**: 스트림 편집기

```bash
# 기본 구조: sed 's/old/new/g' file

sed 's/old/new/' file           # 첫 번째만 치환
sed 's/old/new/g' file          # 전체 치환
sed -i 's/old/new/g' file       # 파일 직접 수정
sed -i.bak 's/old/new/g' file   # 백업 후 수정
sed '3d' file                   # 3번째 줄 삭제
sed '1,5d' file                 # 1-5줄 삭제
sed '/pattern/d' file           # 패턴 매칭 줄 삭제
sed -n '10,20p' file            # 10-20줄만 출력

# 여러 명령
sed -e 's/a/A/g' -e 's/b/B/g' file
```

---

## 4. 파일 권한과 소유권 ◑

### ⭕ Linux 파일 권한 체계 ◑

```
-rwxr-xr-x  1  user  group  4096  Jan 1 12:00  file.txt
│└┬┘└┬┘└┬┘     │      │
│ │  │  │      │      └── 소유 그룹
│ │  │  │      └── 소유자
│ │  │  └── 기타 (others) 권한
│ │  └── 그룹 (group) 권한
│ └── 소유자 (user) 권한
└── 파일 타입 (-: 일반, d: 디렉터리, l: 링크, c: 문자장치, b: 블록장치)
```

### 권한 종류 ◑

| 권한 | 문자 | 숫자 | 파일에서 | 디렉터리에서 |
|------|------|------|----------|--------------|
| 읽기 | r | 4 | 내용 읽기 | 목록 보기 (ls) |
| 쓰기 | w | 2 | 내용 수정 | 파일 생성/삭제/이름변경 |
| 실행 | x | 1 | 실행 | 접근 (cd) |

```
디렉터리 권한의 의미:

r (읽기): ls로 디렉터리 내용 나열 가능
w (쓰기): 디렉터리 내 파일 생성/삭제 가능 (파일 권한과 무관!)
x (실행): 디렉터리에 들어갈 수 있음 (cd)

예시:
  r-x: 목록 보고 들어갈 수 있지만 파일 생성/삭제 불가
  -wx: 들어가서 파일 생성/삭제 가능하지만 목록 못 봄
  rwx: 모든 작업 가능
```

### 권한 변경

```bash
# 문자 방식
chmod u+x file          # 소유자에 실행 권한 추가
chmod g-w file          # 그룹에서 쓰기 권한 제거
chmod o=r file          # 기타에 읽기만 설정
chmod a+r file          # 모두에게 읽기 추가
chmod u=rwx,g=rx,o= file   # 여러 대상 한번에

# 숫자 방식 (8진수)
chmod 755 file          # rwxr-xr-x (실행 파일, 스크립트)
chmod 644 file          # rw-r--r-- (일반 파일)
chmod 600 file          # rw------- (비밀 파일, SSH 키)
chmod 700 dir/          # rwx------ (비공개 디렉터리)
chmod 777 file          # rwxrwxrwx (주의: 보안 위험!)

# 재귀 적용
chmod -R 755 dir/       # 디렉터리 내 모든 파일에 적용
```

### 소유권 변경

```bash
chown user file         # 소유자 변경
chown user:group file   # 소유자 + 그룹 변경
chown :group file       # 그룹만 변경
chown -R user:group dir/   # 재귀적 변경
chgrp group file        # 그룹만 변경 (chown :group과 동일)
```

### ⭕ 특수 권한 ◑

| 권한 | 숫자 | 표시 | 설명 |
|------|------|------|------|
| **SUID** | 4000 | -rw**s** | 실행 시 소유자 권한으로 실행 |
| **SGID** | 2000 | -rwx**s** | 실행 시 그룹 권한으로 / 디렉터리 내 파일 그룹 상속 |
| **Sticky** | 1000 | -rwx**t** | 디렉터리 내 파일 삭제는 소유자만 |

```bash
# SUID 예시: passwd 명령어
ls -l /usr/bin/passwd
-rwsr-xr-x 1 root root ... /usr/bin/passwd
# 일반 사용자도 root 권한으로 비밀번호 변경 가능

# Sticky bit 예시: /tmp 디렉터리
ls -ld /tmp
drwxrwxrwt 10 root root ... /tmp
# 모두 파일 생성 가능하지만, 자신의 파일만 삭제 가능

# 특수 권한 설정
chmod 4755 file         # SUID + rwxr-xr-x
chmod u+s file          # SUID 설정
chmod g+s dir/          # SGID 설정
chmod +t dir/           # Sticky bit 설정
```

### umask ◑

새 파일/디렉터리 생성 시 기본 권한을 결정한다.

```bash
umask               # 현재 umask 확인
umask 022           # umask 설정

# 기본 권한 계산
# 파일: 666 - umask = 기본 권한
# 디렉터리: 777 - umask = 기본 권한

# 예: umask 022
# 파일: 666 - 022 = 644 (rw-r--r--)
# 디렉터리: 777 - 022 = 755 (rwxr-xr-x)
```

---

## 5. 프로세스 관리 ◑

### 프로세스 확인

```bash
ps                      # 현재 터미널의 프로세스
ps aux                  # 모든 프로세스 (BSD 스타일)
ps -ef                  # 모든 프로세스 (System V 스타일)
ps -u username          # 특정 사용자 프로세스
ps aux --sort=-%mem     # 메모리 사용량순

top                     # 실시간 모니터링
htop                    # 향상된 top (설치 필요)

pgrep -f "pattern"      # 패턴으로 PID 찾기
pstree                  # 프로세스 트리
pstree -p               # PID 포함
```

### 프로세스 상태 ◑

```
ps aux 출력의 STAT 열:

R  Running     실행 중 또는 실행 대기
S  Sleeping    대기 중 (인터럽트 가능)
D  Disk sleep  대기 중 (인터럽트 불가, I/O)
T  Stopped     정지됨 (Ctrl+Z)
Z  Zombie      종료됐지만 부모가 수거 안 함
I  Idle        커널 스레드 대기

추가 플래그:
<  high-priority
N  low-priority
s  session leader
l  multi-threaded
+  foreground process group
```

### ⭕ Zombie 프로세스와 Orphan 프로세스 ◑

**Zombie 프로세스**: 종료됐지만 부모가 exit 상태를 수거(wait)하지 않은 프로세스

```
정상적인 프로세스 종료:

부모                    자식
  │                      │
  │ ─────fork()─────────▶│
  │                      │
  │                      ├── 작업 수행
  │                      │
  │                      ├── exit()
  │                      │
  │ ◀────SIGCHLD─────────│  자식 종료
  │                      │
  │ ─────wait()──────────│  exit 상태 수거
  │                      │
  (Zombie 해제)           (프로세스 테이블에서 제거)


Zombie 발생:

부모                    자식
  │                      │
  │                      ├── exit()
  │                      │
  │  (wait() 호출 안 함)   │  ← Zombie 상태!
  │                      │
  (해결: 부모 종료 시 init이 수거)
```

**Orphan 프로세스**: 부모가 먼저 종료되어 고아가 된 프로세스. init(PID 1)이 새 부모가 된다.

```bash
# Zombie 프로세스 확인
ps aux | grep 'Z'

# Zombie 해결: 부모 프로세스를 종료하거나
kill -SIGCHLD <parent_pid>   # 부모에게 자식 수거 요청
```

### 프로세스 제어

```bash
# 실행
command &               # 백그라운드 실행
nohup command &         # 터미널 종료 후에도 실행
nohup command > out.log 2>&1 &   # 출력 파일 지정

# 종료
kill PID                # SIGTERM (정상 종료, 기본값)
kill -9 PID             # SIGKILL (강제 종료)
kill -HUP PID           # SIGHUP (재시작/설정 재로드)
killall name            # 이름으로 종료
pkill -f "pattern"      # 패턴으로 종료

# 작업 제어
jobs                    # 백그라운드 작업 목록
fg %1                   # 백그라운드 → 포그라운드
bg %1                   # 정지된 작업 → 백그라운드
Ctrl+Z                  # 프로세스 정지 (SIGTSTP)
Ctrl+C                  # 프로세스 종료 (SIGINT)
```

### ⭕ 주요 시그널 ◑

| 시그널 | 번호 | 설명 | 처리 가능 |
|--------|------|------|-----------|
| SIGHUP | 1 | 터미널 종료, 재시작 요청 | O |
| SIGINT | 2 | 인터럽트 (Ctrl+C) | O |
| SIGQUIT | 3 | 코어 덤프 생성 종료 (Ctrl+\) | O |
| SIGKILL | 9 | 강제 종료 | **X** |
| SIGSEGV | 11 | 세그멘테이션 폴트 | O |
| SIGTERM | 15 | 정상 종료 (기본값) | O |
| SIGSTOP | 19 | 정지 | **X** |
| SIGCONT | 18 | 재개 | O |
| SIGCHLD | 17 | 자식 프로세스 종료 | O |

---

## 6. 사용자와 그룹 ◑

### 사용자 관리

```bash
# 현재 사용자 정보
whoami                  # 현재 사용자 이름
id                      # UID, GID, 그룹 정보
id username             # 특정 사용자 정보
groups                  # 소속 그룹
users                   # 로그인 사용자
who                     # 로그인 세션 정보
last                    # 로그인 기록

# 사용자 생성/삭제 (root)
useradd -m -s /bin/bash username   # 홈 디렉터리, 셸 지정
useradd -m -G sudo,docker username # 그룹 지정
userdel username         # 사용자 삭제
userdel -r username      # 홈 디렉터리와 함께 삭제
usermod -aG docker username   # 그룹에 추가
passwd username          # 비밀번호 설정/변경

# 권한 상승
su - username           # 사용자 전환 (환경 변수 포함)
su username             # 사용자 전환 (현재 환경 유지)
sudo command            # root 권한으로 실행
sudo -u user command    # 특정 사용자 권한으로 실행
sudo -i                 # root 쉘
sudo -s                 # root 쉘 (환경 유지)
```

### 그룹 관리 ◑

```bash
groupadd groupname      # 그룹 생성
groupdel groupname      # 그룹 삭제
gpasswd -a user group   # 그룹에 사용자 추가
gpasswd -d user group   # 그룹에서 사용자 제거
newgrp group            # 현재 세션의 그룹 변경
```

### 주요 파일 ◑

| 파일 | 내용 | 예시 |
|------|------|------|
| /etc/passwd | 사용자 정보 | `user:x:1000:1000:User Name:/home/user:/bin/bash` |
| /etc/shadow | 암호화된 비밀번호 | `user:$6$...:19000:0:99999:7:::` |
| /etc/group | 그룹 정보 | `docker:x:999:user1,user2` |
| /etc/sudoers | sudo 권한 설정 | `%sudo ALL=(ALL:ALL) ALL` |

```bash
# /etc/passwd 필드 설명
# username:password:UID:GID:GECOS:home:shell
# user:x:1000:1000:User Name:/home/user:/bin/bash

# x는 비밀번호가 /etc/shadow에 있음을 의미
```

---

## 7. 셸과 환경 변수 ◑

### 셸 종류 ◑

| 셸 | 특징 |
|-----|------|
| **bash** | 가장 일반적, POSIX 호환, 스크립트 표준 |
| **zsh** | 강력한 자동완성, Oh My Zsh, macOS 기본 |
| **fish** | 사용자 친화적, 비표준 문법, 자동 제안 |
| **sh** | POSIX 표준, 최소 기능, 이식성 |
| **dash** | 경량, 빠른 실행, Ubuntu의 /bin/sh |

```bash
# 현재 셸 확인
echo $SHELL
echo $0

# 사용 가능한 셸 목록
cat /etc/shells

# 기본 셸 변경
chsh -s /bin/zsh
```

### 셸 시작 파일 로드 순서 ◑

```
로그인 셸 (ssh 접속, 로컬 로그인):
1. /etc/profile
2. ~/.bash_profile 또는 ~/.profile
3. (보통 .bash_profile에서 ~/.bashrc 로드)

비로그인 대화형 셸 (터미널 열기):
1. /etc/bash.bashrc
2. ~/.bashrc

비대화형 셸 (스크립트 실행):
- $BASH_ENV 환경변수가 가리키는 파일
```

```
셸 종류별 시작 흐름:

SSH 로그인
    │
    ▼
┌─────────────────┐
│ /etc/profile    │
├─────────────────┤
│ ~/.bash_profile │ ──▶ source ~/.bashrc
└─────────────────┘

새 터미널 창
    │
    ▼
┌─────────────────┐
│ ~/.bashrc       │
└─────────────────┘
```

### 환경 변수

```bash
echo $PATH              # PATH 확인
echo $HOME              # 홈 디렉터리
echo $USER              # 현재 사용자
env                     # 모든 환경 변수
printenv VAR            # 특정 변수

# 설정 (현재 세션)
export VAR=value
VAR=value               # 자식 프로세스에 전달 안 됨
export VAR              # 이미 있는 변수를 export

# 영구 설정
echo 'export VAR=value' >> ~/.bashrc
source ~/.bashrc        # 적용

# PATH에 추가
export PATH="$PATH:/new/path"
```

### 주요 환경 변수 ◑

| 변수 | 설명 |
|------|------|
| PATH | 실행 파일 검색 경로 |
| HOME | 홈 디렉터리 |
| USER | 현재 사용자 |
| SHELL | 현재 셸 |
| LANG | 언어/로케일 |
| PWD | 현재 작업 디렉터리 |
| OLDPWD | 이전 작업 디렉터리 |
| TERM | 터미널 타입 |
| EDITOR | 기본 텍스트 에디터 |
| PS1 | 프롬프트 형식 |
| LD_LIBRARY_PATH | 동적 라이브러리 경로 |

### 별칭 (Alias) ◑

```bash
# 별칭 확인
alias

# 별칭 설정
alias ll='ls -la'
alias gs='git status'
alias ..='cd ..'
alias ...='cd ../..'

# 영구 설정 (~/.bashrc에 추가)
echo "alias ll='ls -la'" >> ~/.bashrc

# 별칭 해제
unalias ll
```

---

## 8. I/O 리다이렉션과 파이프 ◑

### 파일 디스크립터 ◑

| FD | 이름 | 설명 | 기본 연결 |
|-----|------|------|-----------|
| 0 | stdin | 표준 입력 | 키보드 |
| 1 | stdout | 표준 출력 | 터미널 |
| 2 | stderr | 표준 에러 | 터미널 |

```
프로세스의 I/O 흐름:

          ┌───────────────────────────────────┐
키보드 ──▶│ stdin (0)                         │
          │                                   │
          │           프로세스                │
          │                                   │
          │ stdout (1) ──────────────────────▶│ 터미널
          │ stderr (2) ──────────────────────▶│ 터미널
          └───────────────────────────────────┘
```

### 리다이렉션

```bash
# 출력 리다이렉션
command > file          # stdout → 파일 (덮어쓰기)
command >> file         # stdout → 파일 (추가)
command 2> error.log    # stderr → 파일
command 2>> error.log   # stderr → 파일 (추가)

# stdout과 stderr 모두 리다이렉션
command > out.log 2>&1  # 둘 다 같은 파일로 (순서 중요!)
command &> all.log      # 위와 동일 (bash 단축)
command >> out.log 2>&1 # 추가 모드

# 입력 리다이렉션
command < file          # 파일 → stdin
command << EOF          # Here Document
line 1
line 2
EOF

# 출력 버리기
command > /dev/null     # stdout 버리기
command 2> /dev/null    # stderr 버리기
command &> /dev/null    # 둘 다 버리기

# 파일 디스크립터 조작
exec 3> file            # FD 3을 파일에 연결
echo "text" >&3         # FD 3에 쓰기
exec 3>&-               # FD 3 닫기
```

### 리다이렉션 순서의 중요성 ◑

```bash
# 올바른 순서: 2>&1을 > 뒤에
command > file 2>&1
# 해석: stdout을 file로, stderr를 stdout이 가리키는 곳(file)으로

# 잘못된 순서
command 2>&1 > file
# 해석: stderr를 현재 stdout(터미널)로, stdout을 file로
# 결과: stderr는 터미널에 출력됨
```

### 파이프

```bash
command1 | command2     # 출력을 다음 명령 입력으로
ls -la | grep ".txt"
cat log | grep ERROR | wc -l

# 파이프라인 예시
cat access.log | awk '{print $1}' | sort | uniq -c | sort -rn | head -10

# 파이프와 리다이렉션 조합
command1 2>&1 | command2    # stderr도 파이프로
command1 |& command2        # 위와 동일 (bash)

# tee: 출력을 파일과 stdout 모두로
command | tee file          # 화면에도 출력, 파일에도 저장
command | tee -a file       # 추가 모드
command | tee file1 file2   # 여러 파일
```

### xargs ◑

표준 입력을 명령의 인자로 변환한다.

```bash
# 기본 사용법
find . -name "*.tmp" | xargs rm     # 찾은 파일 삭제

# 안전한 사용 (파일명에 공백 있을 때)
find . -name "*.tmp" -print0 | xargs -0 rm

# 한 번에 처리할 인자 수 지정
cat files.txt | xargs -n 1 process

# 플레이스홀더 사용
cat files.txt | xargs -I {} cp {} /backup/

# 병렬 실행
cat files.txt | xargs -P 4 -I {} process {}
```

### Here Document ◑

```bash
# 기본 사용
cat << EOF
여러 줄의
텍스트를
입력합니다.
변수 확장: $HOME
EOF

# 변수 확장 비활성화
cat << 'EOF'
$HOME은 확장되지 않음
EOF

# 들여쓰기 제거 (<<-)
cat <<- EOF
	탭 문자가 제거됨
	EOF
```

---

## 9. 네트워크 명령어 ◑

### 네트워크 상태

```bash
# IP 주소 확인
ip addr                 # 모든 인터페이스
ip addr show eth0       # 특정 인터페이스
ip -4 addr              # IPv4만
hostname -I             # IP 주소만

# 라우팅
ip route                # 라우팅 테이블
ip route get 8.8.8.8    # 특정 목적지 경로

# 열린 포트 확인
ss -tuln                # TCP/UDP 리스닝 포트
ss -tunp                # 프로세스 정보 포함
ss -s                   # 요약 통계
netstat -tuln           # 레거시

# 연결 상태
ss -tan                 # TCP 연결 상태
ss state established    # 연결된 것만
```

### 연결 테스트

```bash
# ping: ICMP 연결 확인
ping -c 4 host          # 4회만 전송
ping -i 2 host          # 2초 간격

# traceroute: 경로 추적
traceroute host
traceroute -n host      # DNS 역조회 안 함

# curl: HTTP 요청
curl url                # GET 요청
curl -I url             # HEAD (헤더만)
curl -X POST -d "data" url   # POST
curl -o file url        # 파일 저장
curl -L url             # 리다이렉트 따라가기
curl -v url             # 상세 출력
curl -s url             # 조용히

# wget: 파일 다운로드
wget url                # 다운로드
wget -O filename url    # 파일명 지정
wget -c url             # 이어받기

# 포트 연결 테스트
nc -zv host port        # netcat
timeout 3 bash -c '</dev/tcp/host/port && echo open'   # bash 내장
```

### DNS

```bash
nslookup domain         # DNS 조회
nslookup domain 8.8.8.8 # 특정 DNS 서버

dig domain              # 상세 DNS 조회
dig domain MX           # MX 레코드
dig +short domain       # 간략 출력
dig @8.8.8.8 domain     # 특정 DNS 서버

host domain             # 간단 DNS 조회
host -t mx domain       # MX 레코드

# DNS 캐시
systemd-resolve --flush-caches   # systemd
```

### SSH ◑

```bash
# 기본 접속
ssh user@host
ssh -p 2222 user@host   # 포트 지정
ssh -i key.pem user@host   # 키 파일 사용

# 파일 전송
scp file user@host:/path   # 로컬 → 원격
scp user@host:/path file   # 원격 → 로컬
scp -r dir user@host:/path # 디렉터리

# 동기화
rsync -avz src/ user@host:/dst/   # 동기화
rsync -avz --delete src/ dst/      # 삭제 포함 동기화
rsync -avz --exclude='*.log' src/ dst/  # 제외 패턴

# SSH 터널
ssh -L 8080:localhost:80 user@host   # 로컬 포트 포워딩
ssh -R 8080:localhost:80 user@host   # 리모트 포트 포워딩
ssh -D 1080 user@host                # SOCKS 프록시

# SSH 설정 (~/.ssh/config)
# Host myserver
#     HostName server.example.com
#     User admin
#     Port 2222
#     IdentityFile ~/.ssh/mykey
```

### SSH 키 관리 ◑

```bash
# 키 생성
ssh-keygen -t ed25519 -C "email@example.com"
ssh-keygen -t rsa -b 4096 -C "email@example.com"

# 공개키 서버에 복사
ssh-copy-id user@host
# 또는 수동으로
cat ~/.ssh/id_ed25519.pub >> ~/.ssh/authorized_keys

# 권한 설정 (필수)
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub
chmod 600 ~/.ssh/authorized_keys
```

---

## 10. 시스템 정보 ◑

### 시스템 상태

```bash
uname -a                # 시스템 정보 전체
uname -r                # 커널 버전
hostname                # 호스트명
uptime                  # 가동 시간, 부하
date                    # 날짜/시간
timedatectl             # 시간대 정보 (systemd)
cat /etc/os-release     # 배포판 정보
```

### 리소스 사용량

```bash
# 메모리
free -h                 # 사람이 읽기 쉬운 형식
free -m                 # MB 단위

# 디스크
df -h                   # 파일시스템 사용량
df -i                   # inode 사용량
du -sh /path            # 디렉터리 크기
du -h --max-depth=1     # 하위 디렉터리별 크기
ncdu /path              # 대화형 디스크 사용량 (설치 필요)

# CPU
lscpu                   # CPU 정보
nproc                   # CPU 코어 수
mpstat 1                # CPU 사용량 (1초 간격)

# 기타
lsblk                   # 블록 장치
lspci                   # PCI 장치
lsusb                   # USB 장치
lsmem                   # 메모리 블록
dmidecode               # 하드웨어 정보 (root)
```

### 로그 확인

```bash
# 시스템 로그
dmesg                   # 커널 메시지
dmesg -T                # 타임스탬프 포함
dmesg --level=err,warn  # 에러/경고만

# systemd 로그 (journalctl)
journalctl              # 전체 로그
journalctl -u nginx     # 특정 서비스
journalctl -f           # 실시간 추적
journalctl --since "1 hour ago"
journalctl --since "2024-01-01" --until "2024-01-02"
journalctl -p err       # 에러 레벨만
journalctl -b           # 현재 부팅 이후
journalctl -b -1        # 이전 부팅

# 주요 로그 파일
tail -f /var/log/syslog     # 시스템 로그 (Debian)
tail -f /var/log/messages   # 시스템 로그 (RHEL)
tail -f /var/log/auth.log   # 인증 로그
tail -f /var/log/dmesg      # 커널 메시지
```

---

## 11. 서비스 관리 (systemd) ◑

### ⭕ init vs systemd ◑

| 구분 | SysVinit | systemd |
|------|----------|---------|
| 시작 | 순차 실행 | 병렬 실행 (빠른 부팅) |
| 스크립트 | /etc/init.d/ | Unit 파일 |
| 의존성 | 번호로 순서 지정 | 선언적 의존성 |
| 관리 도구 | service, chkconfig | systemctl |
| 프로세스 추적 | PID 파일 | cgroups |
| 로그 | 파일 기반 | journald |

### systemctl 명령어 ◑

```bash
# 서비스 제어
systemctl start nginx       # 시작
systemctl stop nginx        # 중지
systemctl restart nginx     # 재시작
systemctl reload nginx      # 설정만 재로드 (프로세스 유지)
systemctl reload-or-restart nginx

# 상태 확인
systemctl status nginx      # 상세 상태
systemctl is-active nginx   # 실행 여부
systemctl is-enabled nginx  # 부팅 시 시작 여부
systemctl is-failed nginx   # 실패 여부

# 부팅 시 자동 시작
systemctl enable nginx      # 활성화
systemctl disable nginx     # 비활성화
systemctl enable --now nginx  # 활성화 + 즉시 시작

# 목록
systemctl list-units --type=service         # 실행 중인 서비스
systemctl list-units --type=service --all   # 모든 서비스
systemctl list-unit-files --type=service    # 설치된 서비스
systemctl list-dependencies nginx           # 의존성

# 마스킹 (시작 완전 차단)
systemctl mask nginx
systemctl unmask nginx
```

### Unit 파일 ◑

```bash
# Unit 파일 위치
/etc/systemd/system/      # 사용자 정의 (우선순위 높음)
/lib/systemd/system/      # 패키지 설치
/run/systemd/system/      # 런타임 생성

# Unit 파일 예시 (/etc/systemd/system/myapp.service)
```

```ini
[Unit]
Description=My Application
After=network.target

[Service]
Type=simple
User=appuser
WorkingDirectory=/opt/myapp
ExecStart=/opt/myapp/bin/start
ExecStop=/opt/myapp/bin/stop
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
```

```bash
# Unit 파일 수정 후
systemctl daemon-reload     # 데몬 재로드
systemctl restart myapp
```

### 서비스 로그

```bash
journalctl -u nginx          # 서비스 로그
journalctl -u nginx -f       # 실시간 추적
journalctl -u nginx --since "1 hour ago"
journalctl -u nginx -n 100   # 최근 100줄
journalctl -u nginx -o json  # JSON 형식
```

---

## 12. 셸 스크립트 기초 ◑

### 기본 구조

```bash
#!/bin/bash
# 첫 줄은 shebang - 어떤 인터프리터로 실행할지 지정

# 주석
# 변수 (= 앞뒤에 공백 없음!)
NAME="World"
echo "Hello, $NAME"
echo "Hello, ${NAME}!"   # 변수 경계 명확히

# 명령 치환
TODAY=$(date +%Y-%m-%d)
FILES=`ls -la`   # 구식 문법, $() 권장

# 산술 연산
NUM=$((5 + 3))
((NUM++))
```

### 조건문 ◑

```bash
# if 문
if [ "$1" = "test" ]; then
    echo "Test mode"
elif [ -z "$1" ]; then
    echo "No argument"
else
    echo "Argument: $1"
fi

# [[ ]] 사용 (bash 확장, 더 안전)
if [[ "$1" == "test" ]]; then
    echo "Test mode"
fi

# 패턴 매칭 ([[ ]] 에서만)
if [[ "$file" == *.txt ]]; then
    echo "Text file"
fi

# case 문
case "$1" in
    start)
        echo "Starting..."
        ;;
    stop)
        echo "Stopping..."
        ;;
    restart)
        echo "Restarting..."
        ;;
    *)
        echo "Usage: $0 {start|stop|restart}"
        exit 1
        ;;
esac
```

### 반복문 ◑

```bash
# for 문
for i in 1 2 3 4 5; do
    echo $i
done

for i in {1..5}; do
    echo $i
done

for ((i=0; i<5; i++)); do
    echo $i
done

for file in *.txt; do
    echo "Processing $file"
done

# while 문
while [ $count -lt 10 ]; do
    echo $count
    ((count++))
done

# 파일 줄 단위 읽기
while IFS= read -r line; do
    echo "$line"
done < file.txt

# until 문 (조건이 거짓인 동안)
until [ $count -ge 10 ]; do
    echo $count
    ((count++))
done
```

### 함수 ◑

```bash
# 함수 정의
greet() {
    local name="$1"   # 지역 변수
    echo "Hello, $name"
    return 0          # 종료 상태 반환
}

# 함수 호출
greet "User"
result=$(greet "User")   # 출력 캡처

# 함수 종료 상태 확인
if greet "User"; then
    echo "Success"
fi
```

### 테스트 연산자 ◑

| 연산자 | 설명 |
|--------|------|
| -f file | 일반 파일 존재 |
| -d dir | 디렉터리 존재 |
| -e file | 파일/디렉터리 존재 |
| -r file | 읽기 권한 |
| -w file | 쓰기 권한 |
| -x file | 실행 권한 |
| -s file | 파일 크기 > 0 |
| -z str | 문자열이 비어있음 |
| -n str | 문자열이 비어있지 않음 |
| str1 = str2 | 문자열 같음 |
| str1 != str2 | 문자열 다름 |
| num1 -eq num2 | 숫자 같음 |
| num1 -ne num2 | 숫자 다름 |
| num1 -lt num2 | 작음 |
| num1 -le num2 | 작거나 같음 |
| num1 -gt num2 | 큼 |
| num1 -ge num2 | 크거나 같음 |

### 실용 예시 ◑

```bash
#!/bin/bash
# 로그 정리 스크립트

set -e              # 에러 시 종료
set -u              # 미정의 변수 사용 시 에러
set -o pipefail     # 파이프라인 에러 전파

LOG_DIR="${1:-/var/log/myapp}"   # 기본값 설정
DAYS="${2:-30}"

if [ ! -d "$LOG_DIR" ]; then
    echo "Error: Directory $LOG_DIR does not exist"
    exit 1
fi

echo "Cleaning logs older than $DAYS days in $LOG_DIR"

find "$LOG_DIR" -name "*.log" -mtime +$DAYS -type f | while read -r file; do
    echo "Deleting: $file"
    rm -f "$file"
done

echo "Cleanup complete"
```

```bash
#!/bin/bash
# 백업 스크립트

SRC="/data"
DEST="/backup"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="backup_${DATE}.tar.gz"

# 함수 정의
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $*"
}

cleanup() {
    log "Cleaning up old backups..."
    find "$DEST" -name "backup_*.tar.gz" -mtime +7 -delete
}

# 메인 로직
log "Starting backup..."

if tar -czf "${DEST}/${BACKUP_FILE}" "$SRC" 2>/dev/null; then
    log "Backup created: ${BACKUP_FILE}"
    cleanup
else
    log "ERROR: Backup failed!"
    exit 1
fi

log "Backup complete"
```

---

## 13. 패키지 관리 ◑

### Debian/Ubuntu (apt)

```bash
# 패키지 목록 갱신 (설치 전 항상 실행)
apt update

# 패키지 업그레이드
apt upgrade             # 설치된 패키지 업그레이드
apt full-upgrade        # 의존성 해결 포함
apt dist-upgrade        # 배포판 업그레이드

# 설치/삭제
apt install nginx       # 설치
apt install nginx=1.18.0-1   # 버전 지정
apt remove nginx        # 삭제 (설정 유지)
apt purge nginx         # 삭제 (설정 포함)
apt autoremove          # 불필요한 의존성 삭제

# 검색/정보
apt search nginx        # 검색
apt show nginx          # 패키지 정보
apt list --installed    # 설치된 패키지
apt list --upgradable   # 업그레이드 가능

# dpkg (저수준)
dpkg -l                 # 설치된 패키지 목록
dpkg -L nginx           # 패키지 파일 목록
dpkg -S /usr/bin/nginx  # 파일 소속 패키지
dpkg -i package.deb     # .deb 파일 설치
```

### RHEL/CentOS (yum/dnf)

```bash
# dnf는 yum의 후속 (RHEL 8+, Fedora)

yum update              # 전체 업데이트
yum install nginx       # 설치
yum remove nginx        # 삭제
yum search nginx        # 검색
yum info nginx          # 정보
yum list installed      # 설치된 패키지
yum clean all           # 캐시 정리

# RPM (저수준)
rpm -qa                 # 설치된 패키지 목록
rpm -ql nginx           # 패키지 파일 목록
rpm -qf /usr/bin/nginx  # 파일 소속 패키지
rpm -ivh package.rpm    # 설치
```

---

## 14. 아카이브와 압축 ◑

### tar (아카이브)

```bash
# 생성
tar -cvf archive.tar dir/       # 생성 (압축 없음)
tar -czvf archive.tar.gz dir/   # gzip 압축
tar -cjvf archive.tar.bz2 dir/  # bzip2 압축
tar -cJvf archive.tar.xz dir/   # xz 압축

# 해제
tar -xvf archive.tar            # 해제
tar -xvf archive.tar.gz         # gzip 해제 (자동 감지)
tar -xvf archive.tar -C /dest/  # 지정 위치에 해제

# 내용 확인
tar -tvf archive.tar            # 목록 보기

# 옵션
# c: create
# x: extract
# v: verbose
# f: file
# z: gzip
# j: bzip2
# J: xz
# t: list
# C: change directory
```

### 압축 도구 ◑

```bash
# gzip
gzip file               # 압축 (원본 삭제)
gzip -k file            # 원본 유지
gzip -d file.gz         # 해제
gunzip file.gz          # 해제

# bzip2 (더 높은 압축률)
bzip2 file
bunzip2 file.bz2

# xz (최고 압축률)
xz file
unxz file.xz

# zip (윈도우 호환)
zip -r archive.zip dir/
unzip archive.zip
unzip -l archive.zip    # 목록

# 압축률 비교 (일반적으로)
# 크기: gzip > bzip2 > xz
# 속도: gzip > bzip2 > xz
```

---

## 15. 작업 스케줄링 (cron) ◑

### crontab ◑

```bash
crontab -e              # 편집
crontab -l              # 목록 보기
crontab -r              # 삭제
crontab -u user -e      # 다른 사용자 (root)
```

### cron 표현식 ◑

```
분  시  일  월  요일  명령
*   *   *   *   *     /path/to/script

┌───────────── 분 (0 - 59)
│ ┌───────────── 시 (0 - 23)
│ │ ┌───────────── 일 (1 - 31)
│ │ │ ┌───────────── 월 (1 - 12)
│ │ │ │ ┌───────────── 요일 (0 - 6, 일=0)
│ │ │ │ │
* * * * * command
```

```bash
# 예시
0 * * * *     # 매시 정각
*/15 * * * *  # 15분마다
0 2 * * *     # 매일 오전 2시
0 0 * * 0     # 매주 일요일 자정
0 0 1 * *     # 매월 1일 자정
0 0 1 1 *     # 매년 1월 1일 자정

# 실제 사용 예
# 매일 새벽 2시에 백업
0 2 * * * /home/user/backup.sh >> /var/log/backup.log 2>&1

# 5분마다 상태 체크
*/5 * * * * /usr/local/bin/healthcheck.sh
```

### 시스템 cron 디렉터리 ◑

```bash
/etc/crontab           # 시스템 crontab
/etc/cron.d/           # 추가 cron 파일
/etc/cron.hourly/      # 매시간 실행
/etc/cron.daily/       # 매일 실행
/etc/cron.weekly/      # 매주 실행
/etc/cron.monthly/     # 매월 실행
```

### systemd timer (대안) ◑

```bash
# /etc/systemd/system/backup.timer
[Unit]
Description=Daily backup

[Timer]
OnCalendar=*-*-* 02:00:00
Persistent=true

[Install]
WantedBy=timers.target
```

```bash
systemctl enable --now backup.timer
systemctl list-timers
```

---

## 16. Vim 기초 ◑

### 모드 ◑

```
┌─────────────────────────────────────────────┐
│                 Normal 모드                  │
│              (명령 입력)                     │
│                                             │
│    i, a, o                      :, /, ?     │
│       │                             │       │
│       ▼                             ▼       │
│  ┌─────────┐                  ┌──────────┐  │
│  │ Insert  │   Esc            │ Command  │  │
│  │  모드   │ ◀───────────────▶│  모드    │  │
│  └─────────┘      Esc         └──────────┘  │
│                                             │
│       v                                     │
│       │                                     │
│       ▼                                     │
│  ┌─────────┐                                │
│  │ Visual  │                                │
│  │  모드   │                                │
│  └─────────┘                                │
└─────────────────────────────────────────────┘
```

### 기본 명령어 ◑

```bash
# 이동
h j k l         # 왼쪽 아래 위 오른쪽
w b             # 단어 앞으로/뒤로
0 $             # 줄 시작/끝
gg G            # 파일 처음/끝
:n              # n번 줄로 이동

# 편집
i               # 커서 앞에 삽입
a               # 커서 뒤에 삽입
o               # 아래 새 줄
O               # 위에 새 줄
x               # 문자 삭제
dd              # 줄 삭제
yy              # 줄 복사
p               # 붙여넣기
u               # 실행 취소
Ctrl+r          # 다시 실행

# 검색/치환
/pattern        # 앞으로 검색
?pattern        # 뒤로 검색
n N             # 다음/이전 결과
:%s/old/new/g   # 전체 치환
:%s/old/new/gc  # 확인하며 치환

# 저장/종료
:w              # 저장
:q              # 종료
:wq             # 저장 후 종료
:q!             # 저장 없이 강제 종료
:x              # 변경 있으면 저장 후 종료
```

---

## 면접 대비 체크리스트 ◑

- [ ] Linux 시스템 구조 (커널, 셸, 사용자 영역)
- [ ] 파일 시스템 구조 (/, /etc, /var, /home, /proc)
- [ ] 하드 링크 vs 심볼릭 링크
- [ ] 파일 권한 체계 (rwx, chmod, chown)
- [ ] 특수 권한 (SUID, SGID, Sticky bit)
- [ ] umask
- [ ] 프로세스 상태와 라이프사이클
- [ ] Zombie vs Orphan 프로세스
- [ ] 주요 시그널 (SIGTERM, SIGKILL, SIGHUP)
- [ ] 파이프와 리다이렉션 (|, >, >>, 2>&1)
- [ ] 주요 명령어 (find, grep, awk, sed)
- [ ] 환경 변수 (PATH, HOME)
- [ ] 셸 설정 파일 (.bashrc, .profile)
- [ ] 서비스 관리 (systemctl)
- [ ] 셸 스크립트 기초
- [ ] cron 표현식
- [ ] SSH 사용법과 키 관리
