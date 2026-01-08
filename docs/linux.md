# 18. Linux/Unix 기초 (Linux/Unix Fundamentals) ◑

---

## 1. Linux 개요 ◑

### Linux란?

Unix 계열의 오픈소스 운영체제로, 서버, 클라우드, 임베디드 시스템에서 널리 사용된다.

### Unix vs Linux ◑

| 구분 | Unix | Linux |
|------|------|-------|
| 라이선스 | 상용 (AT&T, IBM, Sun) | 오픈소스 (GPL) |
| 커널 | 벤더별 상이 | Linux 커널 |
| 예시 | Solaris, AIX, HP-UX | Ubuntu, CentOS, Debian |
| 철학 | 공유 | Unix 철학 계승 |

### 주요 배포판 ◑

| 계열 | 배포판 | 특징 | 패키지 관리 |
|------|--------|------|------------|
| **Debian** | Ubuntu, Debian | 사용자 친화적 | apt |
| **Red Hat** | RHEL, CentOS, Fedora | 엔터프라이즈 | yum/dnf |
| **Arch** | Arch Linux, Manjaro | 최신 패키지, 미니멀 | pacman |
| **Alpine** | Alpine Linux | 경량, 컨테이너용 | apk |

---

## 2. 파일 시스템 구조 ◑

### 디렉터리 계층 (FHS) ◑

```
/
├── bin/      # 필수 사용자 명령어
├── sbin/     # 시스템 관리 명령어
├── etc/      # 설정 파일
├── home/     # 사용자 홈 디렉터리
├── root/     # root 사용자 홈
├── var/      # 가변 데이터 (로그, 캐시)
├── tmp/      # 임시 파일
├── usr/      # 사용자 프로그램
│   ├── bin/
│   ├── lib/
│   └── local/
├── opt/      # 추가 소프트웨어
├── lib/      # 라이브러리
├── dev/      # 장치 파일
├── proc/     # 프로세스 정보 (가상)
├── sys/      # 시스템 정보 (가상)
└── mnt/      # 마운트 포인트
```

### 주요 디렉터리 ◑

| 디렉터리 | 용도 |
|----------|------|
| **/etc** | 시스템 설정 (nginx.conf, passwd) |
| **/var/log** | 로그 파일 |
| **/home/user** | 사용자 데이터 |
| **/tmp** | 임시 파일 (재부팅 시 삭제) |
| **/proc** | 커널/프로세스 정보 (가상 파일시스템) |

---

## 3. 기본 명령어 ◑

### 파일/디렉터리 조작

```bash
# 이동/확인
pwd                     # 현재 디렉터리
cd /path/to/dir         # 디렉터리 이동
ls -la                  # 목록 (상세, 숨김 포함)
ls -lh                  # 사람이 읽기 쉬운 크기

# 생성/삭제
mkdir -p dir/subdir     # 디렉터리 생성 (-p: 부모 포함)
touch file.txt          # 빈 파일 생성
rm file.txt             # 파일 삭제
rm -rf dir/             # 디렉터리 강제 삭제 (주의!)
rmdir dir/              # 빈 디렉터리 삭제

# 복사/이동
cp src dst              # 복사
cp -r srcdir dstdir     # 디렉터리 복사
mv src dst              # 이동/이름 변경
```

### 파일 내용 확인

```bash
cat file.txt            # 전체 출력
less file.txt           # 페이지 단위 (스크롤)
head -n 20 file.txt     # 처음 20줄
tail -n 20 file.txt     # 마지막 20줄
tail -f file.txt        # 실시간 추적 (로그 모니터링)
```

### 검색

```bash
find /path -name "*.log"           # 파일 검색
find /path -type f -mtime -7       # 7일 내 수정된 파일
grep "pattern" file.txt            # 패턴 검색
grep -r "pattern" /path            # 재귀 검색
grep -i "pattern" file.txt         # 대소문자 무시
grep -v "pattern" file.txt         # 패턴 제외
```

### 텍스트 처리

```bash
wc -l file.txt          # 줄 수
sort file.txt           # 정렬
uniq                    # 중복 제거 (정렬된 입력)
cut -d',' -f1 file.csv  # 필드 추출
awk '{print $1}' file   # 첫 번째 필드 출력
sed 's/old/new/g' file  # 치환
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
└── 파일 타입 (-: 일반, d: 디렉터리, l: 링크)
```

### 권한 종류 ◑

| 권한 | 문자 | 숫자 | 파일 | 디렉터리 |
|------|------|------|------|----------|
| 읽기 | r | 4 | 내용 읽기 | 목록 보기 |
| 쓰기 | w | 2 | 내용 수정 | 파일 생성/삭제 |
| 실행 | x | 1 | 실행 | 접근 (cd) |

### 권한 변경

```bash
# 문자 방식
chmod u+x file          # 소유자에 실행 권한 추가
chmod g-w file          # 그룹에서 쓰기 권한 제거
chmod o=r file          # 기타에 읽기만 설정
chmod a+r file          # 모두에게 읽기 추가

# 숫자 방식 (8진수)
chmod 755 file          # rwxr-xr-x
chmod 644 file          # rw-r--r--
chmod 600 file          # rw------- (비밀 파일)
```

### 소유권 변경

```bash
chown user file         # 소유자 변경
chown user:group file   # 소유자 + 그룹 변경
chown -R user dir/      # 재귀적 변경
chgrp group file        # 그룹만 변경
```

### 특수 권한 ◑

| 권한 | 숫자 | 설명 |
|------|------|------|
| **SUID** | 4000 | 실행 시 소유자 권한으로 실행 |
| **SGID** | 2000 | 실행 시 그룹 권한으로 / 디렉터리 내 파일 그룹 상속 |
| **Sticky** | 1000 | 디렉터리 내 파일 삭제는 소유자만 (/tmp) |

---

## 5. 프로세스 관리 ◑

### 프로세스 확인

```bash
ps aux                  # 모든 프로세스
ps -ef                  # 상세 정보
top                     # 실시간 모니터링
htop                    # 향상된 top
pgrep -f "pattern"      # 패턴으로 PID 찾기
```

### 프로세스 제어

```bash
# 실행
command &               # 백그라운드 실행
nohup command &         # 터미널 종료 후에도 실행

# 종료
kill PID                # SIGTERM (정상 종료)
kill -9 PID             # SIGKILL (강제 종료)
killall name            # 이름으로 종료
pkill -f "pattern"      # 패턴으로 종료

# 작업 제어
jobs                    # 백그라운드 작업 목록
fg %1                   # 백그라운드 → 포그라운드
bg %1                   # 정지된 작업 → 백그라운드
Ctrl+Z                  # 프로세스 정지
Ctrl+C                  # 프로세스 종료
```

### ⭕ 주요 시그널 ◑

| 시그널 | 번호 | 설명 |
|--------|------|------|
| SIGHUP | 1 | 터미널 종료, 재시작 요청 |
| SIGINT | 2 | 인터럽트 (Ctrl+C) |
| SIGKILL | 9 | 강제 종료 (처리 불가) |
| SIGTERM | 15 | 정상 종료 (기본값) |
| SIGSTOP | 19 | 정지 (처리 불가) |
| SIGCONT | 18 | 재개 |

---

## 6. 사용자와 그룹 ◑

### 사용자 관리

```bash
whoami                  # 현재 사용자
id                      # UID, GID, 그룹 정보
users                   # 로그인 사용자

# 사용자 생성/삭제 (root)
useradd -m username     # 홈 디렉터리와 함께 생성
userdel -r username     # 홈 디렉터리와 함께 삭제
passwd username         # 비밀번호 설정/변경

# 권한 상승
su - username           # 사용자 전환
sudo command            # root 권한으로 실행
sudo -i                 # root 쉘
```

### 주요 파일 ◑

| 파일 | 내용 |
|------|------|
| /etc/passwd | 사용자 정보 |
| /etc/shadow | 암호화된 비밀번호 |
| /etc/group | 그룹 정보 |
| /etc/sudoers | sudo 권한 설정 |

---

## 7. 셸과 환경 변수 ◑

### 셸 종류 ◑

| 셸 | 특징 |
|-----|------|
| **bash** | 가장 일반적, 스크립트 호환성 |
| **zsh** | 강력한 자동완성, Oh My Zsh |
| **fish** | 사용자 친화적, 비표준 문법 |
| **sh** | POSIX 표준, 최소 기능 |

### 환경 변수

```bash
echo $PATH              # PATH 확인
echo $HOME              # 홈 디렉터리
env                     # 모든 환경 변수

# 설정
export VAR=value        # 현재 세션
echo 'export VAR=value' >> ~/.bashrc  # 영구 설정
source ~/.bashrc        # 적용
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

### 설정 파일 ◑

| 파일 | 적용 시점 |
|------|----------|
| /etc/profile | 시스템 전역, 로그인 셸 |
| ~/.bash_profile | 사용자별, 로그인 셸 |
| ~/.bashrc | 사용자별, 대화형 셸 |
| ~/.bash_logout | 로그아웃 시 |

---

## 8. I/O 리다이렉션과 파이프 ◑

### 리다이렉션

```bash
command > file          # 출력 → 파일 (덮어쓰기)
command >> file         # 출력 → 파일 (추가)
command < file          # 파일 → 입력
command 2> error.log    # 표준 에러 → 파일
command > out.log 2>&1  # 표준 출력 + 에러
command &> all.log      # 위와 동일 (bash)
```

### 파일 디스크립터 ◑

| FD | 이름 | 설명 |
|-----|------|------|
| 0 | stdin | 표준 입력 |
| 1 | stdout | 표준 출력 |
| 2 | stderr | 표준 에러 |

### 파이프

```bash
command1 | command2     # 출력을 다음 명령 입력으로
ls -la | grep ".txt"    # 예시
cat log | grep ERROR | wc -l  # 체이닝

# 유용한 조합
ps aux | grep nginx
cat access.log | awk '{print $1}' | sort | uniq -c | sort -rn | head
```

### Here Document

```bash
cat << EOF
여러 줄의
텍스트를
입력합니다.
EOF
```

---

## 9. 네트워크 명령어 ◑

### 네트워크 상태

```bash
ip addr                 # IP 주소 확인 (ifconfig 대체)
ip route                # 라우팅 테이블
ss -tuln                # 열린 포트 (netstat 대체)
netstat -tuln           # 레거시
```

### 연결 테스트

```bash
ping host               # ICMP 연결 확인
traceroute host         # 경로 추적
curl url                # HTTP 요청
wget url                # 파일 다운로드
nc -zv host port        # 포트 연결 테스트
telnet host port        # 포트 연결 (레거시)
```

### DNS

```bash
nslookup domain         # DNS 조회
dig domain              # 상세 DNS 조회
host domain             # 간단 DNS 조회
```

### SSH

```bash
ssh user@host           # SSH 접속
ssh -p 2222 user@host   # 포트 지정
ssh -i key.pem user@host  # 키 파일 사용
scp file user@host:/path  # 파일 복사
rsync -avz src/ user@host:/dst/  # 동기화
```

---

## 10. 시스템 정보 ◑

### 시스템 상태

```bash
uname -a                # 시스템 정보
hostname                # 호스트명
uptime                  # 가동 시간
date                    # 날짜/시간
cal                     # 달력
```

### 리소스 사용량

```bash
free -h                 # 메모리 사용량
df -h                   # 디스크 사용량
du -sh /path            # 디렉터리 크기
lscpu                   # CPU 정보
lsblk                   # 블록 장치
```

### 로그 확인

```bash
dmesg                   # 커널 메시지
journalctl              # systemd 로그
journalctl -u nginx     # 특정 서비스 로그
tail -f /var/log/syslog # 시스템 로그
```

---

## 11. 서비스 관리 (systemd) ◑

### systemctl 명령어 ◑

```bash
# 서비스 제어
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
systemctl reload nginx   # 설정만 재로드

# 상태 확인
systemctl status nginx
systemctl is-active nginx
systemctl is-enabled nginx

# 부팅 시 자동 시작
systemctl enable nginx
systemctl disable nginx

# 목록
systemctl list-units --type=service
systemctl list-unit-files
```

### 서비스 로그

```bash
journalctl -u nginx          # 서비스 로그
journalctl -u nginx -f       # 실시간 추적
journalctl -u nginx --since "1 hour ago"
```

---

## 12. 셸 스크립트 기초 ◑

### 기본 구조

```bash
#!/bin/bash

# 변수
NAME="World"
echo "Hello, $NAME"

# 조건문
if [ "$1" = "test" ]; then
    echo "Test mode"
elif [ -z "$1" ]; then
    echo "No argument"
else
    echo "Argument: $1"
fi

# 반복문
for i in 1 2 3; do
    echo $i
done

for file in *.txt; do
    echo "Processing $file"
done

# 함수
greet() {
    echo "Hello, $1"
}
greet "User"
```

### 테스트 연산자 ◑

| 연산자 | 설명 |
|--------|------|
| -f file | 파일 존재 |
| -d dir | 디렉터리 존재 |
| -z str | 문자열이 비어있음 |
| -n str | 문자열이 비어있지 않음 |
| str1 = str2 | 문자열 같음 |
| num1 -eq num2 | 숫자 같음 |
| num1 -lt num2 | 작음 |
| num1 -gt num2 | 큼 |

### 실용 예시

```bash
#!/bin/bash
# 로그 정리 스크립트

LOG_DIR="/var/log/myapp"
DAYS=30

find "$LOG_DIR" -name "*.log" -mtime +$DAYS -delete
echo "Deleted logs older than $DAYS days"
```

---

## 13. 패키지 관리 ◑

### Debian/Ubuntu (apt)

```bash
apt update              # 패키지 목록 갱신
apt upgrade             # 패키지 업그레이드
apt install nginx       # 설치
apt remove nginx        # 삭제
apt search nginx        # 검색
apt show nginx          # 정보
```

### RHEL/CentOS (yum/dnf)

```bash
yum update
yum install nginx
yum remove nginx
yum search nginx
```

---

## 면접 대비 체크리스트 ◑

- [ ] Linux 파일 시스템 구조 (/, /etc, /var, /home)
- [ ] 파일 권한 체계 (rwx, chmod, chown)
- [ ] 프로세스 관리 (ps, kill, 시그널)
- [ ] 파이프와 리다이렉션 (|, >, >>, 2>&1)
- [ ] 주요 명령어 (find, grep, awk, sed)
- [ ] 환경 변수 (PATH, HOME, 설정 파일)
- [ ] 서비스 관리 (systemctl)
- [ ] SSH 사용법
- [ ] 셸 스크립트 기초
