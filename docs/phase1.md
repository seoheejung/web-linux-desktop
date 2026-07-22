# 프로젝트 초기 구성 (Phase 1)

> Webtop 컨테이너 실행에 필요한 프로젝트 설정 파일을 작성하고 Docker Compose 문법과 Git 제외 설정을 검증한다.

## 1. 구현 범위

Phase 1에서 생성하거나 수정할 파일:

```text
compose.yaml
.env.example
.gitignore
workspace/.gitkeep
```

기존 파일 중 수정하지 않을 파일:

```text
README.md
docs/phase1.md
```

로컬에서만 관리할 파일:

```text
.env
```

---

## 2. 제외 범위

Phase 1에서는 아래 항목을 구현하지 않는다.

* Webtop 이미지 다운로드
* Webtop 컨테이너 실행
* 브라우저 HTTPS 접속
* HTTP Basic 인증 확인
* KDE Plasma 화면 확인
* Wayland 또는 X11 세션 확인
* Windows·Linux 파일 공유 검증
* `/config` 데이터 영속성 검증
* 한국어 표시 및 한글 입력 검증
* 클립보드 검증
* 파일 업로드·다운로드 검증
* 오디오 출력 검증
* CPU 및 메모리 측정
* 이미지 digest와 build version 기록
* Dockerfile
* 사용자 정의 Webtop 이미지
* GPU 설정
* 외부 네트워크 접속 설정
* 리버스 프록시
* Docker 소켓 마운트
* 권한 확장 설정
* 백업·복원 자동화
* Phase 2 이후 문서

---

## 3. 완료 구조

```text
web-linux-desktop/
├── .env
├── .env.example
├── .gitignore
├── README.md
├── compose.yaml
├── docs/
│   └── phase1.md
└── workspace/
    └── .gitkeep
```

`.env`는 로컬에 존재하지만 Git 추적 대상에서 제외한다.

---

## 4. 파일별 구현 기준

### 4.1 `compose.yaml`

```yaml
services:
  webtop:
    image: lscr.io/linuxserver/webtop:ubuntu-kde
    container_name: web-linux-desktop
    environment:
      PUID: "1000"
      PGID: "1000"
      TZ: Asia/Seoul
      LC_ALL: ko_KR.UTF-8
      TITLE: Web Linux Desktop
      CUSTOM_USER: ${WEBTOP_USER}
      PASSWORD: ${WEBTOP_PASSWORD}
      FILE_MANAGER_PATH: /workspace
    volumes:
      - webtop_config:/config
      - ./workspace:/workspace
    ports:
      - "127.0.0.1:3001:3001"
    shm_size: "1gb"
    restart: unless-stopped

volumes:
  webtop_config:
```

### Compose 설정 기준

| 설정       | 기준                                      |
| -------- | --------------------------------------- |
| 서비스 이름   | `webtop`                                |
| 이미지      | `lscr.io/linuxserver/webtop:ubuntu-kde` |
| 컨테이너 이름  | `web-linux-desktop`                     |
| 사용자 ID   | `PUID: "1000"`                          |
| 그룹 ID    | `PGID: "1000"`                          |
| 시간대      | `Asia/Seoul`                            |
| 로케일      | `ko_KR.UTF-8`                           |
| 브라우저 제목  | `Web Linux Desktop`                     |
| 인증 사용자명  | `${WEBTOP_USER}`                        |
| 인증 비밀번호  | `${WEBTOP_PASSWORD}`                    |
| 파일 기능 경로 | `/workspace`                            |
| 사용자 데이터  | `webtop_config:/config`                 |
| 공유 파일    | `./workspace:/workspace`                |
| 접속 포트    | `127.0.0.1:3001:3001`                   |
| 공유 메모리   | `1gb`                                   |
| 재시작 정책   | `unless-stopped`                        |

### 포트 기준

* HTTPS `3001`만 연결
* Windows loopback 주소에만 연결
* HTTP `3000` 미노출
* `3001:3001` 형식의 전체 인터페이스 연결 금지

### 금지 설정

```text
privileged: true
security_opt:
  - seccomp:unconfined
devices:
  - /dev/dri:/dev/dri
/var/run/docker.sock:/var/run/docker.sock
PIXELFLUX_WAYLAND
```

---

### 4.2 `.env.example`

```dotenv
WEBTOP_USER=webtop
WEBTOP_PASSWORD=change-me
```

### 환경 변수 기준

* 환경 변수 이름과 형식만 작성
* 실제 사용자명과 비밀번호 미작성
* `WEBTOP_USER`와 `WEBTOP_PASSWORD` 외 변수 추가 금지
* `.env.example` Git 추적

---

### 4.3 `.gitignore`

```gitignore
.env

workspace/*
!workspace/.gitkeep
```

### Git 제외 기준

| 경로                   | 처리 |
| -------------------- | -- |
| `.env`               | 제외 |
| `.env.example`       | 추적 |
| `workspace/.gitkeep` | 추적 |
| `workspace/` 일반 파일   | 제외 |
| `compose.yaml`       | 추적 |
| `docs/phase1.md`     | 추적 |
| `README.md`          | 추적 |

---

### 4.4 `workspace/.gitkeep`

빈 파일로 생성한다.

`workspace/` 디렉터리 구조만 Git에 포함하기 위한 파일이다.

---

## 5. 로컬 환경 변수 준비

Codex 작업 완료 후 사용자가 직접 실행한다.

```powershell
Copy-Item .env.example .env
```

`.env`의 값을 로컬 인증 정보로 변경한다.

```dotenv
WEBTOP_USER=webtop
WEBTOP_PASSWORD=로컬에서-사용할-비밀번호
```

### 관리 기준

* `change-me` 상태로 실행 금지
* `.env` Git 커밋 금지
* 실제 인증 정보 문서 기록 금지
* 실제 인증 정보 Codex 출력 금지
* 사용자명과 비밀번호를 `compose.yaml`에 직접 작성하지 않음

---

## 6. 검증 명령

### Compose 문법 검증

```powershell
docker compose config
```

### `.env` Git 제외 확인

```powershell
git check-ignore -v .env
```

### 공유 파일 Git 제외 확인

```powershell
"workspace ignore check" |
  Set-Content .\workspace\ignore-check.txt

git check-ignore -v .\workspace\ignore-check.txt

Remove-Item .\workspace\ignore-check.txt
```

### Git 상태 확인

```powershell
git status --short
```

### 파일 구조 확인

```powershell
Get-ChildItem -Force -Recurse
```

---

## 7. 정상 기준

### Docker Compose

* `docker compose config` 오류 없음
* `webtop` 서비스 출력
* `ubuntu-kde` 이미지 출력
* 환경 변수 치환 오류 없음
* HTTPS 포트가 `127.0.0.1:3001:3001`
* HTTP `3000` 포트 없음
* `/config` named volume 존재
* `./workspace:/workspace` bind mount 존재
* `FILE_MANAGER_PATH=/workspace`
* Docker 소켓 마운트 없음
* `privileged` 설정 없음
* `seccomp:unconfined` 설정 없음
* GPU 장치 설정 없음
* `PIXELFLUX_WAYLAND` 설정 없음

### Git 제외 설정

* `.env`가 Git 상태에 나타나지 않음
* `.env.example`이 Git 추적 가능 상태
* `workspace/.gitkeep`이 Git 추적 가능 상태
* `workspace/` 일반 파일이 Git 상태에 나타나지 않음

### 작업 범위

* 생성·수정 파일이 Phase 1 범위와 일치
* `README.md` 미수정
* `docs/phase1.md` 미수정
* Phase 2 이후 문서 없음
* Dockerfile 없음
* 백업·복원 스크립트 없음
* 실제 인증 정보가 Git 추적 파일에 없음
* 실행하지 않은 Phase 2 결과가 문서에 없음

---

## 8. 완료 기준

* [ ] `compose.yaml` 작성
* [ ] `.env.example` 작성
* [ ] `.gitignore` 작성
* [ ] `workspace/.gitkeep` 생성
* [ ] 로컬 `.env` 생성
* [ ] `.env`에 로컬 인증 정보 작성
* [ ] `.env` Git 제외 확인
* [ ] `workspace/` 일반 파일 Git 제외 확인
* [ ] `workspace/.gitkeep` Git 추적 가능 확인
* [ ] `/config` named volume 설정
* [ ] `./workspace:/workspace` bind mount 설정
* [ ] `FILE_MANAGER_PATH=/workspace` 설정
* [ ] HTTPS `127.0.0.1:3001:3001`만 연결
* [ ] HTTP `3000` 포트 미노출
* [ ] Docker 소켓 미마운트
* [ ] `privileged` 모드 미사용
* [ ] `seccomp:unconfined` 미설정
* [ ] GPU 장치 미마운트
* [ ] `PIXELFLUX_WAYLAND` 미설정
* [ ] `docker compose config` 통과
* [ ] Phase 2 이후 파일 미생성
* [ ] Phase 1 범위 밖 변경 없음
* [ ] 실제 인증 정보가 Git 추적 파일에 없음
* [ ] 실행하지 않은 Phase 2 결과가 문서에 없음
