# Linux 데스크톱 사용 및 파일 공유 (Phase 3)

> Linux 데스크톱에서 Windows 공유 디렉터리와 양방향으로 파일을 교환하고, 컨테이너 재시작과 재생성 후 `/config`, `/workspace`, KDE 사용자 설정이 유지되는지 확인한다.

## 1. 구현 범위

Phase 3에서 수행할 작업:

```text
Webtop 컨테이너 실행 상태 확인
/config 사용자 홈 디렉터리 확인
/workspace 공유 디렉터리 확인
/config와 /workspace 마운트 유형 확인
Linux에서 공유 파일 생성
Windows에서 Linux 생성 파일 확인
Windows에서 공유 파일 생성
Linux에서 Windows 생성 파일 확인
Linux에서 한글 파일명 생성
Windows에서 Linux 한글 파일명 확인
Windows에서 한글 파일명 생성
Linux에서 Windows 한글 파일명 확인
/config 영속성 검증 파일 생성
KDE 사용자 설정 변경
컨테이너 재시작
재시작 후 /config 유지 확인
재시작 후 /workspace 유지 확인
재시작 후 KDE 사용자 설정 유지 확인
컨테이너 재생성
재생성 후 /config 유지 확인
재생성 후 /workspace 유지 확인
재생성 후 KDE 사용자 설정 유지 확인
실행 결과 기록
```

생성하거나 수정할 파일:

```text
docs/phase3.md
```

기존 파일 중 수정하지 않을 파일:

```text
README.md
compose.yaml
.env.example
.gitignore
docs/phase1.md
docs/phase2.md
workspace/.gitkeep
```

파일 공유 검증 과정에서 생성하는 로컬 파일:

```text
workspace/linux-created.txt
workspace/windows-created.txt
workspace/리눅스-생성.txt
workspace/윈도우-생성.txt
```

공유 검증 파일은 `.gitignore`의 `workspace/*` 규칙에 따라 Git 추적 대상에서 제외한다.

---

## 2. 제외 범위

Phase 3에서는 아래 내용을 구현하지 않는다.

* 한국어 UI 전체 적용 검증
* KDE 한글 입력기 설치
* KDE 한글 입력 검증
* Windows와 Webtop 사이의 클립보드 검증
* Webtop 파일 업로드·다운로드 검증
* 오디오 출력 검증
* CPU 및 메모리 측정
* 파일 I/O 성능 측정
* 대규모 소스 트리 공유
* 패키지 설치
* Discover PackageKit 오류 수정
* Dockerfile
* 사용자 정의 Webtop 이미지
* GPU 설정
* 외부 네트워크 접속 설정
* 리버스 프록시
* Docker 소켓 마운트
* `privileged` 모드
* `seccomp:unconfined`
* `PIXELFLUX_WAYLAND` 강제 설정
* 백업·복원 자동화
* Docker named volume 삭제
* Phase 4 문서
* README 최종 결과 작성

Phase 2에서 확인된 HTTPS 접속, HTTP Basic 인증, KDE Plasma 화면, Linux 애플리케이션 실행 결과는 Phase 3에서 다시 독립적인 구현 항목으로 처리하지 않는다.

---

## 3. 완료 구조

Git에서 관리하는 Phase 3 완료 구조:

```text
web-linux-desktop/
├── .env
├── .env.example
├── .gitignore
├── README.md
├── compose.yaml
├── docs/
│   ├── phase1.md
│   ├── phase2.md
│   └── phase3.md
└── workspace/
    └── .gitkeep
```

로컬 `workspace`에는 검증 파일이 존재할 수 있다.

```text
workspace/
├── .gitkeep
├── linux-created.txt
├── windows-created.txt
├── 리눅스-생성.txt
└── 윈도우-생성.txt
```

공유 검증 파일은 로컬에는 유지되지만 Git 상태에는 표시되지 않아야 한다.

Phase 3에서는 기존 Compose 실행 설정을 변경하지 않는다.

---

## 4. 실행 및 검증

### 4.1 Webtop 컨테이너 상태 확인

Windows PowerShell에서 프로젝트 루트 기준으로 실행한다.

```powershell
docker compose ps
```

### 확인 기준

* 서비스 이름이 `webtop`
* 컨테이너 이름이 `web-linux-desktop`
* 컨테이너가 실행 상태
* HTTPS 포트가 `127.0.0.1:3001->3001/tcp`
* 반복 재시작 상태가 아님

컨테이너가 실행 중이 아니면 시작한다.

```powershell
docker compose up -d
```

브라우저에서 기존 접속 주소로 KDE Plasma에 접속한다.

```text
https://localhost:3001
```

---

### 4.2 `/config`와 `/workspace` 경로 확인

Webtop KDE 터미널에서 실행한다.

```bash
pwd
echo "${HOME:-unknown}"
ls -ld /config
ls -la /config
ls -ld /workspace
ls -la /workspace
```

### 확인 기준

| 항목           | 확인 내용                     |
| ------------ | ------------------------- |
| `pwd`        | 터미널 현재 경로                 |
| `HOME`       | Webtop 사용자 홈 경로           |
| `/config`    | Webtop 사용자 데이터와 KDE 설정 경로 |
| `/workspace` | Windows와 Linux 사이의 공유 경로  |

`HOME` 출력과 `/config` 경로 관계를 실제 결과로 기록한다.

Windows PowerShell에서 마운트 유형을 확인한다.

```powershell
docker inspect web-linux-desktop `
  --format '{{range .Mounts}}{{println .Type .Destination}}{{end}}'
```

### 확인 기준

```text
volume /config
bind /workspace
```

실제 Docker volume 이름과 Windows 절대 경로는 실행 환경에 따라 달라질 수 있다.

---

### 4.3 Linux에서 공유 파일 생성

Webtop KDE 터미널에서 실행한다.

```bash
echo "created from linux desktop" > /workspace/linux-created.txt
```

Linux에서 파일을 확인한다.

```bash
ls -la /workspace/linux-created.txt
cat /workspace/linux-created.txt
```

### 기대 내용

```text
created from linux desktop
```

---

### 4.4 Windows에서 Linux 생성 파일 확인

Windows PowerShell에서 실행한다.

```powershell
Test-Path .\workspace\linux-created.txt
Get-Content .\workspace\linux-created.txt
```

### 기대 결과

```text
True
created from linux desktop
```

Linux의 경로:

```text
/workspace/linux-created.txt
```

Windows의 경로:

```text
.\workspace\linux-created.txt
```

두 경로는 같은 bind mount 파일을 가리킨다.

---

### 4.5 Windows에서 공유 파일 생성

Windows PowerShell에서 실행한다.

```powershell
"created from windows" |
  Set-Content .\workspace\windows-created.txt -Encoding utf8
```

Windows에서 파일을 확인한다.

```powershell
Test-Path .\workspace\windows-created.txt
Get-Content .\workspace\windows-created.txt
```

### 기대 결과

```text
True
created from windows
```

---

### 4.6 Linux에서 Windows 생성 파일 확인

Webtop KDE 터미널에서 실행한다.

```bash
ls -la /workspace/windows-created.txt
cat /workspace/windows-created.txt
```

### 기대 내용

```text
created from windows
```

파일명 표시와 파일 내용 출력을 각각 확인한다.

---

### 4.7 Linux에서 한글 파일명 생성

Webtop KDE 터미널에서 실행한다.

```bash
echo "한글 파일명 검증" > /workspace/리눅스-생성.txt
```

Linux에서 확인한다.

```bash
ls -la /workspace
cat /workspace/리눅스-생성.txt
```

### 기대 내용

```text
한글 파일명 검증
```

이 검증은 한글 파일명 표시와 접근 여부를 확인하는 작업이다.

KDE 한글 입력기 동작 검증은 Phase 4 범위다.

---

### 4.8 Windows에서 Linux 한글 파일명 확인

Windows PowerShell에서 실행한다.

```powershell
Test-Path .\workspace\리눅스-생성.txt
Get-Content .\workspace\리눅스-생성.txt
```

### 기대 결과

```text
True
한글 파일명 검증
```

Windows 파일 탐색기에서도 `리눅스-생성.txt`가 정상적으로 표시되는지 확인한다.

---

### 4.9 Windows에서 한글 파일명 생성

Windows PowerShell에서 실행한다.

```powershell
"윈도우 생성 파일" |
  Set-Content .\workspace\윈도우-생성.txt -Encoding utf8
```

Windows에서 확인한다.

```powershell
Test-Path .\workspace\윈도우-생성.txt
Get-Content .\workspace\윈도우-생성.txt
```

### 기대 결과

```text
True
윈도우 생성 파일
```

---

### 4.10 Linux에서 Windows 한글 파일명 확인

Webtop KDE 터미널에서 실행한다.

```bash
ls -la /workspace
cat /workspace/윈도우-생성.txt
```

### 기대 내용

```text
윈도우 생성 파일
```

KDE 파일 관리자에서도 한글 파일명을 확인한다.

```text
리눅스-생성.txt
윈도우-생성.txt
```

---

### 4.11 `/config` 영속성 검증 파일 생성

Webtop KDE 터미널에서 실행한다.

```bash
echo "persistent config test" > /config/persistence-test.txt
```

파일을 확인한다.

```bash
ls -la /config/persistence-test.txt
cat /config/persistence-test.txt
```

### 기대 내용

```text
persistent config test
```

---

### 4.12 KDE 사용자 설정 변경

컨테이너 재시작과 재생성 후 설정 유지 여부를 확인할 수 있도록 KDE 사용자 설정 하나를 변경한다.

검증 대상:

```text
KDE 바탕화면 배경
```

현재 상태와 구분되는 바탕화면 배경으로 변경한다.

### 기록 기준

| 항목      | 결과      |
| ------- | ------- |
| 변경한 설정  | 바탕화면 배경 |
| 변경 전 상태 | 실행 후 기록 |
| 변경 후 상태 | 실행 후 기록 |

외부 테마, 패키지, 플러그인은 설치하지 않는다.

---

### 4.13 컨테이너 재시작

Windows PowerShell에서 실행한다.

```powershell
docker compose restart webtop
docker compose ps
```

컨테이너가 실행 상태로 전환된 뒤 브라우저에 다시 접속한다.

```text
https://localhost:3001
```

Webtop KDE 터미널에서 확인한다.

```bash
cat /config/persistence-test.txt
cat /workspace/linux-created.txt
cat /workspace/windows-created.txt
cat /workspace/리눅스-생성.txt
cat /workspace/윈도우-생성.txt
```

KDE 바탕화면 배경이 변경된 상태로 유지되는지 확인한다.

### 확인 기준

* `/config/persistence-test.txt` 내용 유지
* `/workspace` 영문 파일 유지
* `/workspace` 한글 파일명 유지
* 공유 파일 내용 유지
* KDE 바탕화면 설정 유지
* 브라우저 재접속 가능

---

### 4.14 컨테이너 재생성

Windows PowerShell에서 실행한다.

```powershell
docker compose down
docker compose up -d
docker compose ps
```

`docker compose down`은 컨테이너와 Compose 네트워크를 제거한다.

`-v` 또는 `--volumes` 옵션을 사용하지 않으면 Compose named volume은 제거하지 않는다.

컨테이너가 실행 상태가 된 뒤 브라우저에 다시 접속한다.

Webtop KDE 터미널에서 확인한다.

```bash
cat /config/persistence-test.txt
cat /workspace/linux-created.txt
cat /workspace/windows-created.txt
cat /workspace/리눅스-생성.txt
cat /workspace/윈도우-생성.txt
```

경로 상태를 확인한다.

```bash
ls -la /config
ls -la /workspace
```

KDE 바탕화면 배경이 변경된 상태로 유지되는지 확인한다.

Windows PowerShell에서도 공유 파일을 확인한다.

```powershell
Get-ChildItem .\workspace

Get-Content .\workspace\linux-created.txt
Get-Content .\workspace\windows-created.txt
Get-Content .\workspace\리눅스-생성.txt
Get-Content .\workspace\윈도우-생성.txt
```

### 확인 기준

* 컨테이너 재생성 성공
* `/config/persistence-test.txt` 유지
* `/workspace` 영문 파일 유지
* `/workspace` 한글 파일명 유지
* 공유 파일 내용 유지
* KDE 바탕화면 설정 유지
* 브라우저 재접속 가능

---

### 4.15 Git 제외 및 변경 범위 확인

Windows PowerShell에서 실행한다.

```powershell
git check-ignore -v .\workspace\linux-created.txt
git check-ignore -v .\workspace\windows-created.txt
git check-ignore -v .\workspace\리눅스-생성.txt
git check-ignore -v .\workspace\윈도우-생성.txt
```

Git 상태를 확인한다.

```powershell
git status --short
```

### 확인 기준

* 공유 검증 파일에 `workspace/*` 제외 규칙 적용
* 공유 검증 파일이 Git 상태에 표시되지 않음
* `.env`가 Git 상태에 표시되지 않음
* `compose.yaml` 변경 없음
* Phase 4 파일 없음
* Phase 3 범위 밖 변경 없음

---

## 5. 실행 결과 기록

### 경로 및 마운트 정보

| 항목                  | 결과      |
| ------------------- | ------- |
| 터미널 현재 경로           | 실행 후 기록 |
| `HOME`              | 실행 후 기록 |
| `/config` 접근        | 실행 후 기록 |
| `/workspace` 접근     | 실행 후 기록 |
| `/config` 마운트 유형    | 실행 후 기록 |
| `/workspace` 마운트 유형 | 실행 후 기록 |

### 파일 공유 정보

| 방향              | 파일                    | 파일명 표시  | 내용 확인   | 결과      |
| --------------- | --------------------- | ------- | ------- | ------- |
| Linux → Windows | `linux-created.txt`   | 실행 후 기록 | 실행 후 기록 | 실행 후 기록 |
| Windows → Linux | `windows-created.txt` | 실행 후 기록 | 실행 후 기록 | 실행 후 기록 |
| Linux → Windows | `리눅스-생성.txt`          | 실행 후 기록 | 실행 후 기록 | 실행 후 기록 |
| Windows → Linux | `윈도우-생성.txt`          | 실행 후 기록 | 실행 후 기록 | 실행 후 기록 |

### 컨테이너 재시작 정보

| 항목                              | 결과      |
| ------------------------------- | ------- |
| `docker compose restart webtop` | 실행 후 기록 |
| 컨테이너 상태                         | 실행 후 기록 |
| HTTPS 재접속                       | 실행 후 기록 |
| `/config/persistence-test.txt`  | 실행 후 기록 |
| `/workspace` 영문 파일              | 실행 후 기록 |
| `/workspace` 한글 파일명             | 실행 후 기록 |
| KDE 사용자 설정                      | 실행 후 기록 |

### 컨테이너 재생성 정보

| 항목                             | 결과      |
| ------------------------------ | ------- |
| `docker compose down`          | 실행 후 기록 |
| `docker compose up -d`         | 실행 후 기록 |
| 컨테이너 상태                        | 실행 후 기록 |
| HTTPS 재접속                      | 실행 후 기록 |
| `/config/persistence-test.txt` | 실행 후 기록 |
| `/workspace` 영문 파일             | 실행 후 기록 |
| `/workspace` 한글 파일명            | 실행 후 기록 |
| KDE 사용자 설정                     | 실행 후 기록 |

### Git 제외 정보

| 항목                    | 결과      |
| --------------------- | ------- |
| `linux-created.txt`   | 실행 후 기록 |
| `windows-created.txt` | 실행 후 기록 |
| `리눅스-생성.txt`          | 실행 후 기록 |
| `윈도우-생성.txt`          | 실행 후 기록 |
| `.env`                | 실행 후 기록 |

### 발생한 문제

```text
작업 중 문제가 발생한 경우 실제 오류와 조치 기록
```

### 최종 상태

```text
실행 후 실제 결과 기록
```

---

## 6. 주의사항

* Phase 2 완료 상태에서 Phase 3 검증 시작
* 실제 사용자명과 비밀번호 기록 금지
* `/config`와 `/workspace` 역할 혼동 금지
* `/config`에 Windows 공유 파일 저장 금지
* `/workspace`에 KDE 사용자 설정 저장 금지
* 파일명 표시와 파일 내용 출력을 구분해서 기록
* 한글 파일명 검증을 KDE 한글 입력 검증으로 처리하지 않음
* Windows 텍스트 파일 생성 시 UTF-8 인코딩 명시
* 컨테이너 재시작과 재생성을 구분
* `docker compose down -v` 실행 금지
* `docker compose down --volumes` 실행 금지
* `docker volume rm` 실행 금지
* `docker volume prune` 실행 금지
* 영속성 결과 확인 전 volume 삭제 금지
* 기존 Compose 설정 변경 금지
* 권한 오류 발생 시 `privileged` 즉시 추가 금지
* 권한 오류 발생 시 `seccomp:unconfined` 즉시 추가 금지
* 파일 공유 문제 발생 시 Docker 소켓 또는 GPU 설정 추가 금지
* Windows bind mount 성능 결과 임의 작성 금지
* Phase 4 기능과 결과 선행 작성 금지

---

## 7. 정상 기준

### 경로

* Webtop 사용자 홈과 `/config` 관계 확인
* `/config` 접근 가능
* `/workspace` 접근 가능
* `/config`가 Docker volume으로 연결됨
* `/workspace`가 Windows bind mount로 연결됨

### 파일 공유

* Linux에서 생성한 파일을 Windows에서 읽을 수 있음
* Windows에서 생성한 파일을 Linux에서 읽을 수 있음
* Linux에서 생성한 한글 파일명이 Windows에서 표시됨
* Windows에서 생성한 한글 파일명이 Linux에서 표시됨
* 영문 파일 내용이 양쪽 환경에서 동일함
* 한글 파일 내용이 양쪽 환경에서 동일함

### 컨테이너 재시작

* Webtop 컨테이너 재시작 성공
* 브라우저 재접속 가능
* `/config` 테스트 파일 유지
* `/workspace` 공유 파일 유지
* 한글 파일명 유지
* KDE 사용자 설정 유지

### 컨테이너 재생성

* Webtop 컨테이너 재생성 성공
* 브라우저 재접속 가능
* `/config` 테스트 파일 유지
* `/workspace` 공유 파일 유지
* 한글 파일명 유지
* KDE 사용자 설정 유지

### 프로젝트 범위

* Docker volume 삭제 명령 미사용
* Compose 설정 변경 없음
* 공유 검증 파일 Git 제외
* Phase 4 파일 없음
* Phase 3 범위 밖 변경 없음

---

## 8. 완료 기준

* [ ] `docs/phase3.md` 작성
* [ ] Webtop 컨테이너 실행 상태 확인
* [ ] `/config` 사용자 홈 경로 확인
* [ ] `/config` 접근 확인
* [ ] `/workspace` 접근 확인
* [ ] `/config` volume mount 확인
* [ ] `/workspace` bind mount 확인
* [ ] Linux에서 `linux-created.txt` 생성
* [ ] Windows에서 `linux-created.txt` 확인
* [ ] Windows에서 `windows-created.txt` 생성
* [ ] Linux에서 `windows-created.txt` 확인
* [ ] Linux에서 `리눅스-생성.txt` 생성
* [ ] Windows에서 `리눅스-생성.txt` 확인
* [ ] Windows에서 `윈도우-생성.txt` 생성
* [ ] Linux에서 `윈도우-생성.txt` 확인
* [ ] 한글 파일명 양방향 표시 확인
* [ ] 영문 파일 내용 양방향 확인
* [ ] 한글 파일 내용 양방향 확인
* [ ] `/config/persistence-test.txt` 생성
* [ ] KDE 사용자 설정 변경
* [ ] 컨테이너 재시작 성공
* [ ] 재시작 후 HTTPS 재접속
* [ ] 재시작 후 `/config` 파일 유지
* [ ] 재시작 후 `/workspace` 파일 유지
* [ ] 재시작 후 한글 파일명 유지
* [ ] 재시작 후 KDE 사용자 설정 유지
* [ ] 컨테이너 재생성 성공
* [ ] 재생성 후 HTTPS 재접속
* [ ] 재생성 후 `/config` 파일 유지
* [ ] 재생성 후 `/workspace` 파일 유지
* [ ] 재생성 후 한글 파일명 유지
* [ ] 재생성 후 KDE 사용자 설정 유지
* [ ] 공유 검증 파일 Git 제외 확인
* [ ] Docker volume 삭제 명령 미사용
* [ ] README 미수정
* [ ] Compose 설정 미수정
* [ ] Phase 4 문서 미생성
* [ ] Phase 3 범위 밖 변경 없음
