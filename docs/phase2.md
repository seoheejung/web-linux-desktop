# Webtop 실행 및 브라우저 접속 (Phase 2)

> Webtop 컨테이너를 실행하고 Windows 브라우저에서 KDE Plasma 화면과 실제 데스크톱 세션 유형을 확인한다.

## 1. 구현 범위

Phase 2에서 수행할 작업:

```text
Webtop 이미지 다운로드
이미지 Repo digest 확인
이미지 build version 확인
Webtop 컨테이너 실행
컨테이너 build version 확인
컨테이너 상태 확인
컨테이너 재시작 횟수 확인
시작 로그 확인
Windows 브라우저 HTTPS 접속
자체 서명 인증서 경고 확인
브라우저 HTTP Basic 인증
KDE Plasma 화면 확인
Linux 애플리케이션 실행
Linux 환경 정보 확인
실제 데스크톱 세션 유형 확인
컨테이너 AVX2 플래그 감지 여부 확인
실행 결과 기록
```

생성하거나 수정할 파일:

```text
docs/phase2.md
```

기존 파일 중 수정하지 않을 파일:

```text
README.md
compose.yaml
.env.example
.gitignore
docs/phase1.md
workspace/.gitkeep
```

로컬 `.env`의 실제 사용자명과 비밀번호는 문서와 실행 결과에 기록하지 않는다.

---

## 2. 제외 범위

Phase 2에서는 아래 항목을 구현하지 않는다.

* Windows·Linux 파일 공유 검증
* `/config` 데이터 영속성 검증
* 컨테이너 재생성 후 데이터 유지 검증
* 한국어 UI 전체 적용 검증
* 한글 입력 검증
* 클립보드 검증
* 파일 업로드·다운로드 검증
* 오디오 출력 검증
* CPU 및 메모리 측정
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
* Phase 3 이후 문서
* README 최종 결과 작성

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
│   ├── phase1.md
│   └── phase2.md
└── workspace/
    └── .gitkeep
```

Phase 2에서는 기존 실행 설정을 변경하지 않는다.

---

## 4. 실행 및 검증

### 4.1 Compose 구성 확인

```powershell
docker compose config
```

### 확인 기준

* `webtop` 서비스 존재
* 이미지가 `lscr.io/linuxserver/webtop:ubuntu-kde`
* 컨테이너 이름이 `web-linux-desktop`
* HTTPS 포트가 `127.0.0.1:3001:3001`
* HTTP `3000` 포트 없음
* `/config` named volume 존재
* `./workspace:/workspace` bind mount 존재
* `PIXELFLUX_WAYLAND` 설정 없음
* 권한 확장 설정 없음

---

### 4.2 Webtop 이미지 다운로드

```powershell
docker compose pull
```

---

### 4.3 이미지 Repo digest 확인

```powershell
docker image inspect `
  --format '{{json .RepoDigests}}' `
  lscr.io/linuxserver/webtop:ubuntu-kde
```

Compose에는 `ubuntu-kde` 태그를 유지한다.

검증 시점에 출력된 Repo digest만 기록한다.

---

### 4.4 이미지 build version 확인

```powershell
docker image inspect `
  -f '{{ index .Config.Labels "build_version" }}' `
  lscr.io/linuxserver/webtop:ubuntu-kde
```

출력되지 않은 값을 임의로 작성하지 않는다.

---

### 4.5 Webtop 컨테이너 실행

```powershell
docker compose up -d
```

---

### 4.6 컨테이너 상태 확인

```powershell
docker compose ps
```

### 확인 기준

* 서비스 이름이 `webtop`
* 컨테이너 이름이 `web-linux-desktop`
* 컨테이너 실행 상태
* `127.0.0.1:3001->3001/tcp` 포트 연결
* 반복 재시작 없음

---

### 4.7 컨테이너 재시작 횟수 확인

```powershell
docker inspect `
  -f '{{.RestartCount}}' `
  web-linux-desktop
```

### 확인 기준

* `RestartCount`가 `0`
* 재시작 이력이 있으면 실제 숫자 기록
* 반복 재시작 여부를 `docker compose ps` 상태와 함께 판단

---

### 4.8 시작 로그 확인

```powershell
docker compose logs --tail 200 webtop
```

### 확인 기준

* 초기화 로그 출력
* 반복되는 치명적 오류 없음
* 지속적인 컨테이너 재시작 없음
* 인증 정보 노출 없음

---

### 4.9 컨테이너 build version 확인

```powershell
docker inspect `
  -f '{{ index .Config.Labels "build_version" }}' `
  web-linux-desktop
```

이미지 build version과 컨테이너 build version을 개별 기록한다.

---

### 4.10 Windows 브라우저 접속

```text
https://localhost:3001
```

### 접속 순서

```text
HTTPS 접속
→ 자체 서명 인증서 경고 확인
→ 인증서 경고 통과
→ 브라우저 HTTP Basic 인증 대화상자 확인
→ .env에 설정한 사용자명과 비밀번호 입력
→ KDE Plasma 화면 확인
```

사용자명과 비밀번호를 요구하는 화면은 `브라우저 HTTP Basic 인증 대화상자`로 기록한다.

`Webtop 인증 화면`, `Webtop 로그인 페이지`, `KDE 로그인 화면` 표현은 사용하지 않는다.

---

### 4.11 KDE Plasma 동작 확인

브라우저에서 직접 확인한다.

* KDE Plasma 바탕화면
* 애플리케이션 메뉴
* 터미널
* 파일 관리자
* Linux 브라우저
* 마우스 입력
* 키보드 입력
* 창 이동
* 창 크기 변경

각 항목은 실제 조작 후 결과를 기록한다.

---

### 4.12 Linux 환경 정보 확인

Webtop 터미널에서 실행한다.

```bash
cat /etc/os-release
uname -a
echo "${XDG_CURRENT_DESKTOP:-unknown}"
echo "${XDG_SESSION_TYPE:-unknown}"
locale
grep -m 1 -o 'avx2' /proc/cpuinfo || true
```

### 확인 기준

| 명령                    | 확인 내용               |
| --------------------- | ------------------- |
| `cat /etc/os-release` | Linux 사용자 공간 정보     |
| `uname -a`            | 컨테이너에서 확인되는 커널 정보   |
| `XDG_CURRENT_DESKTOP` | 데스크톱 환경             |
| `XDG_SESSION_TYPE`    | 실제 세션 유형            |
| `locale`              | 로케일 환경              |
| `avx2` 검색             | 컨테이너 AVX2 플래그 감지 여부 |

---

### 4.13 실제 세션 유형 확인

```bash
echo "${XDG_SESSION_TYPE:-unknown}"
```

기록 가능한 값:

```text
wayland
x11
unknown
```

`unknown`이면 환경 변수와 프로세스를 추가 확인한다.

```bash
printenv |
  grep -E 'XDG_CURRENT_DESKTOP|XDG_SESSION_TYPE|WAYLAND_DISPLAY|DISPLAY|PIXELFLUX_WAYLAND' ||
  true
```

```bash
ps -ef |
  grep -Ei 'wayland|x11|xorg|xwayland|kwin' |
  grep -v grep ||
  true
```

필요한 경우 Windows PowerShell에서 컨테이너 로그를 다시 확인한다.

```powershell
docker compose logs --tail 200 webtop
```

### 세션 기록 기준

* 실제 출력 전 Wayland 또는 X11로 확정하지 않음
* X11 세션을 실패로 처리하지 않음
* `unknown`을 즉시 실패로 처리하지 않음
* AVX2 플래그 감지 결과만으로 X11 폴백 원인을 확정하지 않음
* `PIXELFLUX_WAYLAND` 명시 설정은 `없음`으로 기록
* X11 폴백 여부는 환경 변수, 프로세스, 로그를 근거로 판정
* 판정 근거가 부족하면 `확인 불가`로 기록

---

### 4.14 컨테이너 AVX2 플래그 감지 여부 확인

```bash
grep -m 1 -o 'avx2' /proc/cpuinfo || true
```

### 기록 기준

| 출력        | 기록    |
| --------- | ----- |
| `avx2` 출력 | 감지    |
| 출력 없음     | 미감지   |
| 명령 실패     | 확인 실패 |

이 결과는 컨테이너의 `/proc/cpuinfo`에 AVX2 플래그가 노출되는지 확인한 결과다.

출력 없음만으로 호스트 CPU가 AVX2를 지원하지 않는다고 단정하지 않는다.

---

## 5. 실행 결과 기록

### 실행 이미지 정보

| 항목                 | 결과                                      |
| ------------------ | --------------------------------------- |
| 이미지                | `lscr.io/linuxserver/webtop:ubuntu-kde` |
| Repo digest        | 실행 후 기록                                 |
| 이미지 build version  | 실행 후 기록                                 |
| 컨테이너 build version | 실행 후 기록                                 |
| 확인 날짜              | 실행 후 기록                                 |

### 컨테이너 실행 정보

| 항목           | 결과                  |
| ------------ | ------------------- |
| 서비스          | `webtop`            |
| 컨테이너         | `web-linux-desktop` |
| 실행 상태        | 실행 후 기록             |
| RestartCount | 실행 후 기록             |
| 반복 재시작       | 실행 후 기록             |
| HTTPS 포트     | `127.0.0.1:3001`    |
| 시작 로그 오류     | 실행 후 기록             |

### 브라우저 접속 정보

| 항목                 | 결과      |
| ------------------ | ------- |
| HTTPS 접속           | 실행 후 기록 |
| 자체 서명 인증서 경고       | 실행 후 기록 |
| HTTP Basic 인증 대화상자 | 실행 후 기록 |
| 설정한 인증 정보로 인증      | 실행 후 기록 |
| KDE Plasma 화면      | 실행 후 기록 |

실제 사용자명과 비밀번호는 기록하지 않는다.

### 데스크톱 세션 정보

| 항목                        | 결과                          |
| ------------------------- | --------------------------- |
| Desktop                   | 실행 후 기록                     |
| Session type              | `wayland`, `x11`, `unknown` |
| 컨테이너 AVX2 플래그             | 감지 / 미감지 / 확인 실패            |
| `PIXELFLUX_WAYLAND` 명시 설정 | 없음                          |
| X11 폴백 여부                 | 해당 없음 / 확인 / 확인 불가          |
| 판정 근거                     | 환경 변수 / 프로세스 / 로그           |

### KDE 동작 정보

| 확인 항목      | 결과      |
| ---------- | ------- |
| 애플리케이션 메뉴  | 실행 후 기록 |
| 터미널        | 실행 후 기록 |
| 파일 관리자     | 실행 후 기록 |
| Linux 브라우저 | 실행 후 기록 |
| 마우스 입력     | 실행 후 기록 |
| 키보드 입력     | 실행 후 기록 |
| 창 이동       | 실행 후 기록 |
| 창 크기 변경    | 실행 후 기록 |

### Linux 환경 정보

| 항목           | 결과               |
| ------------ | ---------------- |
| 운영체제         | 실행 후 기록          |
| 커널           | 실행 후 기록          |
| Desktop      | 실행 후 기록          |
| Session type | 실행 후 기록          |
| 로케일          | 실행 후 기록          |
| AVX2 플래그     | 감지 / 미감지 / 확인 실패 |

필요한 경우 실제 명령 출력 전체를 함께 기록한다.

```text
실행 후 실제 출력 기록
```

---

## 6. 주의사항

* 인증서 경고와 서버 접속 실패를 구분
* 실제 사용자명과 비밀번호 기록 금지
* `Webtop 인증 화면` 표현 사용 금지
* 실제 세션 확인 전 Wayland 또는 X11 결과 작성 금지
* `Wayland 전용으로 실행됨` 단정 금지
* X11 세션 자동 실패 처리 금지
* `XDG_SESSION_TYPE=unknown` 즉시 실패 처리 금지
* AVX2 플래그 미감지를 호스트 CPU의 AVX2 미지원으로 단정 금지
* digest와 build version 임의 작성 금지
* 접속 문제 발생 시 `seccomp:unconfined` 즉시 추가 금지
* 접속 문제 발생 시 `PIXELFLUX_WAYLAND=false` 즉시 추가 금지
* 로그 확인 없이 Compose 설정 변경 금지
* KDE 화면 확인 전 완료 처리 금지
* X11 폴백 여부를 근거 없이 확정하지 않음
* Phase 3 이후 결과 선행 작성 금지

---

## 7. 정상 기준

### 컨테이너

* Webtop 이미지 다운로드 성공
* 이미지 Repo digest 확인
* 이미지 build version 확인
* 컨테이너 build version 확인
* 컨테이너 실행 상태
* `RestartCount` 확인
* 반복 재시작 없음
* 시작 로그에 반복 치명적 오류 없음
* HTTPS 포트가 Windows loopback에 연결됨

### 브라우저

* `https://localhost:3001` 접속 가능
* 자체 서명 인증서 경고 확인
* 브라우저 HTTP Basic 인증 대화상자 표시
* 설정한 사용자명과 비밀번호로 인증 성공
* KDE Plasma 화면 표시

### KDE Plasma

* 애플리케이션 메뉴 사용 가능
* 터미널 실행 가능
* 파일 관리자 실행 가능
* Linux 브라우저 실행 가능
* 마우스와 키보드 입력 가능
* 창 이동과 크기 변경 가능

### 세션 정보

* Linux 환경 정보 출력
* 운영체제 정보 기록
* 커널 정보 기록
* 로케일 정보 기록
* `XDG_CURRENT_DESKTOP` 기록
* `XDG_SESSION_TYPE` 기록
* 컨테이너 AVX2 플래그 감지 여부 기록
* 실제 Wayland 또는 X11 세션에서 KDE 동작 확인
* X11 폴백 여부와 판정 근거 기록

---

## 8. 완료 기준

* [ ] `docs/phase2.md` 작성
* [ ] `docker compose config` 통과
* [ ] Webtop 이미지 다운로드
* [ ] 이미지 Repo digest 기록
* [ ] 이미지 build version 기록
* [ ] 컨테이너 build version 기록
* [ ] Webtop 컨테이너 실행 성공
* [ ] 컨테이너 실행 상태 확인
* [ ] 컨테이너 `RestartCount` 확인
* [ ] 컨테이너 반복 재시작 없음
* [ ] 시작 로그 확인
* [ ] 반복 치명적 오류 없음
* [ ] HTTPS 접속 성공
* [ ] 자체 서명 인증서 경고 확인
* [ ] 브라우저 HTTP Basic 인증 대화상자 표시
* [ ] 설정한 사용자명과 비밀번호로 인증 성공
* [ ] KDE Plasma 화면 표시
* [ ] 애플리케이션 메뉴 확인
* [ ] 터미널 실행
* [ ] 파일 관리자 실행
* [ ] Linux 브라우저 실행
* [ ] 마우스 입력 확인
* [ ] 키보드 입력 확인
* [ ] 창 이동 확인
* [ ] 창 크기 변경 확인
* [ ] Linux 환경 정보 확인
* [ ] 운영체제 정보 기록
* [ ] 커널 정보 기록
* [ ] 로케일 정보 기록
* [ ] `XDG_CURRENT_DESKTOP` 기록
* [ ] `XDG_SESSION_TYPE` 기록
* [ ] 컨테이너 AVX2 플래그 감지 여부 기록
* [ ] Wayland 또는 X11 세션의 KDE 동작 확인
* [ ] X11 폴백 여부와 판정 근거 기록
* [ ] 실행 이미지 정보 기록
* [ ] 데스크톱 세션 정보 기록
* [ ] README 미수정
* [ ] Phase 3 이후 문서 미생성
* [ ] Phase 2 범위 밖 변경 없음
