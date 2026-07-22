# 사용성 및 자원 검증 (Phase 4)

> 웹 브라우저 기반 Linux 데스크톱을 실제로 조작하고 기능별 결과와 컨테이너 자원 사용량을 기록한다.

## 1. 구현 범위

Phase 4에서 수행할 작업:

```text
Webtop 컨테이너 실행 상태 확인
실제 데스크톱 세션 유형 재확인
화면 해상도 확인
전체 화면 진입과 해제 확인
창 이동 확인
창 크기 변경 확인
영문 입력 확인
한국어 표시 확인
한글 파일명 표시 확인
한글 입력 확인
사용 입력기 확인
Windows와 Webtop 사이의 클립보드 확인
Webtop 파일 업로드 확인
Webtop 파일 다운로드 확인
오디오 출력 확인
컨테이너 상세 상태 확인
컨테이너 프로세스 확인
KDE 초기 화면 자원 측정
터미널 실행 상태 자원 측정
브라우저 실행 상태 자원 측정
파일 관리자 실행 상태 자원 측정
오디오 재생 상태 자원 측정
컨테이너 중지
컨테이너 재실행
재실행 후 HTTPS 접속 확인
README 최종 정리
실행 결과 기록
```

Phase 4에서 생성할 파일:

```text
docs/phase4.md
```

모든 검증이 완료된 뒤 수정할 파일:

```text
README.md
```

수정하지 않을 파일:

```text
compose.yaml
.env.example
.gitignore
docs/phase1.md
docs/phase2.md
docs/phase3.md
workspace/.gitkeep
```

`docs/phase1.md`, `docs/phase2.md`, `docs/phase3.md`는 완료된 Phase의 구현 및 검증 기준이므로 수정하지 않는다.

Phase 4 검증 과정에서 사용할 로컬 파일:

```text
workspace/phase4-download-test.txt
```

Windows에서 준비할 업로드 원본 파일:

```text
phase4-upload-test.txt
```

업로드 원본 파일은 프로젝트의 `workspace` 외부에서 준비한다.

```text
Windows 바탕화면
Windows 문서 폴더
Windows 다운로드 폴더
```

`workspace` 내부에 업로드 원본을 만들면 bind mount로 인해 업로드 전부터 `/workspace`에 표시될 수 있으므로 사용하지 않는다.

---

## 2. Phase 연결 기준

Phase 4는 Phase 1~3 완료 상태에서 시작한다.

```text
Phase 1
→ Docker Compose와 Git 제외 구성 완료

Phase 2
→ Webtop 실행, HTTPS 접속, KDE Plasma와 세션 확인 완료

Phase 3
→ Windows·Linux 파일 공유와 데이터 영속성 확인 완료

Phase 4
→ 사용성, 브라우저 연동 기능, 자원 사용량, stop·start 검증
```

Phase 4에서는 Phase 1~3의 검증 절차를 다시 수행하지 않는다.

Phase 3에서 생성한 공유 파일은 한국어 표시와 한글 파일명 확인에 사용할 수 있다.

```text
workspace/리눅스-생성.txt
workspace/윈도우-생성.txt
```

이 파일을 사용하는 것은 Phase 3 파일 공유 검증을 다시 수행하는 작업이 아니다.

Phase 4에서는 KDE 화면에서 한글 파일명이 정상적으로 표시되는지만 확인한다.

---

## 3. 제외 범위

Phase 4에서는 이 작업을 진행하지 않는다.

* `compose.yaml` 변경
* `.env` 변경
* `.env` 내용 확인 또는 출력
* Webtop 이미지 변경
* Webtop 이미지 업데이트
* Dockerfile 작성
* 사용자 정의 Webtop 이미지 작성
* 패키지 설치
* 한글 입력기 신규 설치
* 입력기 패키지 변경
* Discover PackageKit 오류 수정
* GPU 가속
* GPU 장치 마운트
* Docker 소켓 마운트
* `privileged` 모드
* `seccomp:unconfined`
* `PIXELFLUX_WAYLAND` 설정
* HTTP `3000` 포트 노출
* 외부 인터페이스 포트 연결
* 외부 네트워크 공개
* 리버스 프록시 구성
* CPU 제한 추가
* 메모리 제한 추가
* CPU 권장값 작성
* 메모리 권장값 작성
* 성능 최적화
* 파일 I/O 성능 측정
* 대규모 소스 트리 성능 측정
* 자동 측정 스크립트 작성
* 자동 이미지 업데이트
* 백업·복원 자동화
* Docker volume 삭제
* `docs/phase1.md` 수정
* `docs/phase2.md` 수정
* `docs/phase3.md` 수정
* 실제로 측정하지 않은 결과 작성

사용성 검증 중 문제가 발생해도 기존 실행 구성을 변경하지 않는다.

실패한 항목은 실제 오류와 발생 조건을 기록한다.

---

## 4. 완료 구조

Phase 4 완료 시 Git에서 관리하는 구조:

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
│   ├── phase3.md
│   └── phase4.md
└── workspace/
    └── .gitkeep
```

로컬 `workspace`에는 기존 검증 파일과 Phase 4 다운로드 검증 파일이 존재할 수 있다.

```text
workspace/
├── .gitkeep
├── linux-created.txt
├── windows-created.txt
├── 리눅스-생성.txt
├── 윈도우-생성.txt
└── phase4-download-test.txt
```

`workspace` 검증 파일은 `.gitignore`의 `workspace/*` 규칙에 따라 Git 상태에 표시되지 않아야 한다.

---

# 실행 및 검증

## 1. Webtop 컨테이너 실행 상태 확인

Windows PowerShell에서 프로젝트 루트 기준으로 실행한다.

```powershell
docker compose ps
```

### 확인 기준

* 서비스 이름이 `webtop`
* 컨테이너 이름이 `web-linux-desktop`
* 컨테이너 상태가 `Up`
* HTTPS 포트가 `127.0.0.1:3001->3001/tcp`
* 반복 재시작 상태가 아님

컨테이너가 실행 중이 아니면 시작한다.

```powershell
docker compose up -d
docker compose ps
```

브라우저에서 접속한다.

```text
https://localhost:3001
```

### 확인 기준

* 자체 서명 인증서 경고 통과 가능
* 브라우저 HTTP Basic 인증 가능
* KDE Plasma 화면 표시

Phase 2에서 확인한 접속 결과를 복사하지 않고 Phase 4 시작 시점의 실제 접속 상태를 확인한다.

---

## 2. 실제 데스크톱 세션 유형 재확인

Webtop KDE 터미널에서 실행한다.

```bash
echo "${XDG_SESSION_TYPE:-unknown}"
```

기록 가능한 값:

```text
wayland
x11
unknown
```

### 판정 기준

| 출력        | 기록       |
| --------- | -------- |
| `wayland` | Wayland  |
| `x11`     | X11      |
| `unknown` | 확인 실패    |
| 그 외 값     | 실제 출력 기록 |

* Phase 2 결과를 Phase 4 결과로 복사하지 않음
* 실제 출력 전 Wayland 또는 X11로 확정하지 않음
* X11을 실패로 처리하지 않음
* `unknown`을 임의로 Wayland 또는 X11로 변경하지 않음

### 결과 기록

| 항목           | 결과                                    |
| ------------ | ------------------------------------- |
| 확인 명령        | `echo "${XDG_SESSION_TYPE:-unknown}"` |
| Session type | 실행 후 기록                               |
| 확인 시각        | 실행 후 기록                               |
| 판정           | 실행 후 기록                               |

---

## 3. 화면 해상도 확인

KDE Plasma의 디스플레이 설정을 연다.

```text
애플리케이션 메뉴
→ 시스템 설정
→ 디스플레이 및 모니터
→ 현재 해상도 확인
```

### 확인 대상

* 현재 화면 해상도
* 화면 배율
* 화면 잘림 여부
* KDE 화면 표시 상태

### 결과 기록

| 항목        | 결과       |
| --------- | -------- |
| 화면 해상도    | 실행 후 기록  |
| 화면 배율     | 실행 후 기록  |
| 화면 잘림     | 있음 / 없음  |
| KDE 화면 표시 | 정상 / 비정상 |
| 비고        | 실행 후 기록  |

화면 해상도는 KDE 설정 화면에 표시된 실제 값을 기록한다.

---

## 4. 전체 화면 전환 확인

현재 Webtop 화면에서 제공되는 전체 화면 기능을 실행한다.

```text
KDE Plasma 화면 표시
→ 전체 화면 진입
→ 화면 표시 확인
→ 마우스 입력 확인
→ 키보드 입력 확인
→ 전체 화면 해제
→ 일반 화면 복귀 확인
```

### 결과 기록

| 항목       | 결과       |
| -------- | -------- |
| 전체 화면 진입 | 성공 / 실패  |
| 전체 화면 표시 | 정상 / 비정상 |
| 화면 잘림    | 있음 / 없음  |
| 마우스 입력   | 성공 / 실패  |
| 키보드 입력   | 성공 / 실패  |
| 전체 화면 해제 | 성공 / 실패  |
| 일반 화면 복귀 | 성공 / 실패  |
| 오류       | 실행 후 기록  |

브라우저 자체 전체 화면과 Webtop 화면 전체 화면을 혼동하지 않도록 실제 사용한 기능을 비고에 기록한다.

---

## 5. 창 이동 확인

KDE 터미널, Dolphin 파일 관리자, 내부 브라우저 중 하나를 실행한다.

```text
애플리케이션 창 실행
→ 제목 표시줄 선택
→ 화면 내부의 다른 위치로 이동
→ 창 표시와 입력 상태 확인
```

### 결과 기록

| 항목           | 결과       |
| ------------ | -------- |
| Session type | 실행 후 기록  |
| 확인 애플리케이션    | 실행 후 기록  |
| 창 이동         | 성공 / 실패  |
| 이동 후 화면 표시   | 정상 / 비정상 |
| 화면 깨짐        | 있음 / 없음  |
| 입력 문제        | 있음 / 없음  |
| 오류           | 실행 후 기록  |

---

## 6. 창 크기 변경 확인

실행 중인 KDE 애플리케이션 창의 가장자리 또는 모서리를 드래그한다.

```text
창 가장자리 선택
→ 창 크기 확대
→ 내부 화면 재배치 확인
→ 창 크기 축소
→ 내부 화면 재배치 확인
```

### 결과 기록

| 항목           | 결과       |
| ------------ | -------- |
| Session type | 실행 후 기록  |
| 확인 애플리케이션    | 실행 후 기록  |
| 창 확대         | 성공 / 실패  |
| 창 축소         | 성공 / 실패  |
| 내부 화면 재배치    | 정상 / 비정상 |
| 화면 깨짐        | 있음 / 없음  |
| 오류           | 실행 후 기록  |

---

## 7. 영문 입력 확인

KDE 터미널 또는 텍스트 편집기를 실행한다.

검증 문자열:

```text
web-linux-desktop phase4 input test 123
```

### 확인 대상

* 영문 소문자 입력
* 영문 대문자 입력
* 숫자 입력
* 공백 입력
* 백스페이스 입력
* Enter 입력

### 결과 기록

| 항목        | 결과      |
| --------- | ------- |
| 확인 애플리케이션 | 실행 후 기록 |
| 영문 소문자    | 성공 / 실패 |
| 영문 대문자    | 성공 / 실패 |
| 숫자        | 성공 / 실패 |
| 공백        | 성공 / 실패 |
| 백스페이스     | 성공 / 실패 |
| Enter     | 성공 / 실패 |
| 오류        | 실행 후 기록 |

---

## 8. 한국어 표시 확인

Webtop KDE 터미널에서 실행한다.

```bash
printf '한국어 표시 확인\n'
```

Dolphin 파일 관리자에서 `/workspace`를 연다.

확인 대상:

```text
리눅스-생성.txt
윈도우-생성.txt
```

필요한 경우 Kate 편집기에서 파일을 열어 한글 내용을 확인한다.

### 확인 기준

* 터미널에서 한글 문자열 표시
* KDE 메뉴의 한국어 표시 상태
* Dolphin에서 한글 파일명 표시
* Kate에서 한글 파일 내용 표시
* 글자 깨짐 여부

### 결과 기록

| 항목            | 결과              |
| ------------- | --------------- |
| 터미널 한글 출력     | 성공 / 실패         |
| KDE 메뉴 한국어 표시 | 성공 / 실패 / 일부 적용 |
| 한글 파일명 표시     | 성공 / 실패         |
| 한글 파일 내용 표시   | 성공 / 실패         |
| 글자 깨짐         | 있음 / 없음         |
| 오류            | 실행 후 기록         |

Phase 3에서 한글 파일명 공유는 이미 검증했다.

Phase 4에서는 KDE 화면의 한글 표시 상태만 기록한다.

---

## 9. 한글 입력과 입력기 확인

KDE 터미널 또는 텍스트 편집기를 실행한다.

검증 문자열:

```text
한글 입력 확인
```

### 확인 순서

```text
한글 입력 상태 전환
→ 한글 입력 확인 입력
→ 자음과 모음 조합 확인
→ 띄어쓰기 확인
→ 백스페이스 확인
→ 영문 입력 상태 복귀
```

### 사용 입력기 확인

KDE 패널 또는 입력기 표시 영역에서 사용 중인 입력기 이름을 확인한다.

확인할 수 없으면 다음 값으로 기록한다.

```text
확인되지 않음
```

한글 입력에 실패하더라도 입력기나 패키지를 설치하지 않는다.

### 결과 기록

| 항목        | 결과       |
| --------- | -------- |
| 확인 애플리케이션 | 실행 후 기록  |
| 한글 입력     | 성공 / 실패  |
| 한글 조합     | 정상 / 비정상 |
| 띄어쓰기      | 성공 / 실패  |
| 백스페이스     | 성공 / 실패  |
| 한·영 전환    | 성공 / 실패  |
| 사용 입력기    | 실행 후 기록  |
| 추가 설정     | 없음       |
| 오류        | 실행 후 기록  |

---

## 10. 클립보드 공유 확인

### Windows에서 Webtop으로 복사

Windows에서 검증 문자열을 복사한다.

```text
windows-to-webtop-clipboard
```

Webtop KDE 터미널 또는 텍스트 편집기에 붙여넣는다.

### Webtop에서 Windows로 복사

Webtop에서 검증 문자열을 복사한다.

```text
webtop-to-windows-clipboard
```

Windows 메모장에 붙여넣는다.

### 확인 흐름

```text
Windows 텍스트 복사
→ Webtop에 붙여넣기
→ 문자열 일치 확인
→ Webtop 텍스트 복사
→ Windows 메모장에 붙여넣기
→ 문자열 일치 확인
```

### 결과 기록

| 방향               | 검증 문자열                        | 결과      |
| ---------------- | ----------------------------- | ------- |
| Windows → Webtop | `windows-to-webtop-clipboard` | 실행 후 기록 |
| Webtop → Windows | `webtop-to-windows-clipboard` | 실행 후 기록 |

문자열 일부만 붙여넣어지거나 변형되면 실제 현상을 기록한다.

---

## 11. 파일 업로드 확인

Windows에서 업로드 원본 파일을 준비한다.

파일명:

```text
phase4-upload-test.txt
```

파일 내용:

```text
phase4 file upload test
```

업로드 원본은 프로젝트의 `workspace` 외부에 저장한다.

허용되는 원본 위치:

```text
Windows 바탕화면
Windows 문서 폴더
Windows 다운로드 폴더
```

### 업로드 전 확인

Webtop KDE 터미널에서 실행한다.

```bash
if [ -e /workspace/phase4-upload-test.txt ]; then
  echo "upload target already exists"
else
  echo "upload target not present"
fi
```

기대 결과:

```text
upload target not present
```

파일이 이미 존재하면 기존 파일을 제거한 뒤 업로드를 시작한다.

```bash
rm -f /workspace/phase4-upload-test.txt
```

### 업로드 흐름

```text
Webtop 사이드바 파일 기능 열기
→ Windows의 phase4-upload-test.txt 선택
→ 업로드 실행
→ /workspace에서 파일 확인
→ 파일 내용 확인
```

Webtop KDE 터미널에서 확인한다.

```bash
ls -la /workspace/phase4-upload-test.txt
cat /workspace/phase4-upload-test.txt
```

기대 내용:

```text
phase4 file upload test
```

### 결과 기록

| 항목                 | 결과      |
| ------------------ | ------- |
| 업로드 원본 위치          | 실행 후 기록 |
| 업로드 전 대상 파일        | 없음 / 있음 |
| Webtop 파일 기능 실행    | 성공 / 실패 |
| 업로드 실행             | 성공 / 실패 |
| `/workspace` 파일 확인 | 성공 / 실패 |
| 파일 내용 확인           | 성공 / 실패 |
| 오류                 | 실행 후 기록 |

업로드 원본을 `workspace` 내부에 생성하지 않는다.

---

## 12. 파일 다운로드 확인

Webtop KDE 터미널에서 다운로드 검증 파일을 생성한다.

```bash
echo "phase4 file download test" > /workspace/phase4-download-test.txt
```

파일 내용을 확인한다.

```bash
ls -la /workspace/phase4-download-test.txt
cat /workspace/phase4-download-test.txt
```

기대 내용:

```text
phase4 file download test
```

Windows 다운로드 폴더에 같은 이름의 기존 파일이 있으면 검증 전에 제거하거나 파일명을 구분한다.

### 다운로드 흐름

```text
Webtop 사이드바 파일 기능 열기
→ /workspace/phase4-download-test.txt 선택
→ 다운로드 실행
→ Windows 다운로드 폴더 확인
→ 다운로드된 파일명 확인
→ 다운로드된 파일 내용 확인
```

Windows PowerShell에서 실제 다운로드 경로를 사용해 확인한다.

```powershell
Test-Path "<Windows-다운로드-경로>\phase4-download-test.txt"
Get-Content "<Windows-다운로드-경로>\phase4-download-test.txt"
```

### 결과 기록

| 항목                 | 결과                                    |
| ------------------ | ------------------------------------- |
| 원본 파일              | `/workspace/phase4-download-test.txt` |
| Webtop 파일 기능 실행    | 성공 / 실패                               |
| 다운로드 실행            | 성공 / 실패                               |
| Windows 다운로드 파일 확인 | 성공 / 실패                               |
| 파일 내용 확인           | 성공 / 실패                               |
| 오류                 | 실행 후 기록                               |

`.\workspace\phase4-download-test.txt`는 bind mount 원본이다.

Windows 다운로드 폴더의 파일을 다운로드 결과로 확인해야 한다.

---

## 13. 오디오 출력 확인

Webtop 내부 브라우저를 실행한다.

```text
오디오가 포함된 콘텐츠 열기
→ 콘텐츠 재생
→ Webtop 음소거 상태 확인
→ Windows 출력 장치에서 소리 확인
→ 재생 중 화면 조작 확인
→ 재생 중지
```

### 확인 대상

* 내부 브라우저 실행
* 오디오 콘텐츠 재생
* Windows 출력 장치에서 오디오 출력
* 소리 끊김 여부
* 재생 중 화면 조작 가능 여부

### 결과 기록

| 항목             | 결과      |
| -------------- | ------- |
| 내부 브라우저 실행     | 성공 / 실패 |
| 콘텐츠 재생         | 성공 / 실패 |
| Windows 오디오 출력 | 성공 / 실패 |
| 소리 끊김          | 있음 / 없음 |
| 재생 중 화면 조작     | 성공 / 실패 |
| Windows 출력 장치  | 실행 후 기록 |
| 오류             | 실행 후 기록 |

---

# 자원 측정

## 1. 측정 기준

Webtop 공식 최소 CPU와 메모리 요구량은 확인되지 않았다.

임의 기준값을 작성하지 않고 실제 측정값만 기록한다.

각 상태에서 확인할 값:

```text
CPU %
MEM USAGE / LIMIT
MEM %
Session type
측정 시각
실행 상태
```

측정 대상:

```text
web-linux-desktop 컨테이너
```

`docker stats` 값은 Windows 호스트 전체의 CPU와 메모리 사용량이 아니다.

다른 측정 상태의 값을 복사하거나 재사용하지 않는다.

---

## 2. 측정 상태 공통 기준

각 측정은 대상 애플리케이션만 실행한 상태에서 진행한다.

```text
이전 측정에서 실행한 애플리케이션 종료
→ 측정 대상 애플리케이션 실행
→ 상태 확인
→ docker stats 실행
→ 실제 값 기록
→ docker stats 종료
```

### 상태별 실행 기준

| 측정 상태     | 실행 조건                        |
| --------- | ---------------------------- |
| KDE 초기 화면 | 추가 애플리케이션 없음                 |
| 터미널 실행    | KDE 터미널 1개만 실행               |
| 브라우저 실행   | 내부 브라우저 1개와 웹 페이지 1개만 실행     |
| 파일 관리자 실행 | Dolphin 1개에서 `/workspace` 표시 |
| 오디오 재생    | 내부 브라우저 1개에서 오디오 콘텐츠 재생      |

오디오 재생 상태를 제외한 측정에서는 오디오를 재생하지 않는다.

---

## 3. 컨테이너 상세 상태 확인

전체 `docker inspect web-linux-desktop` 출력에는 컨테이너 환경 변수와 인증 정보가 포함될 수 있다.

필요한 항목만 형식화해 확인한다.

Windows PowerShell:

```powershell
docker inspect web-linux-desktop `
  --format 'Status={{.State.Status}} RestartCount={{.RestartCount}} Image={{.Config.Image}}'
```

마운트 상태를 함께 확인할 경우:

```powershell
docker inspect web-linux-desktop `
  --format '{{range .Mounts}}{{println .Type .Destination}}{{end}}'
```

### 확인 기준

* 컨테이너 상태 출력
* RestartCount 출력
* 실행 이미지 출력
* `/config` volume mount 출력
* `/workspace` bind mount 출력
* 실제 인증 정보 미출력

### 결과 기록

| 항목               | 결과      |
| ---------------- | ------- |
| 컨테이너 상태          | 실행 후 기록 |
| RestartCount     | 실행 후 기록 |
| 실행 이미지           | 실행 후 기록 |
| `/config` 마운트    | 실행 후 기록 |
| `/workspace` 마운트 | 실행 후 기록 |
| 오류               | 실행 후 기록 |

전체 JSON 출력을 파일이나 문서에 저장하지 않는다.

---

## 4. 컨테이너 프로세스 확인

Windows PowerShell:

```powershell
docker top web-linux-desktop
```

### 확인 대상

* 컨테이너 프로세스 목록 출력
* KDE 관련 프로세스 출력
* Webtop 관련 프로세스 출력
* 명령 오류 여부

### 결과 기록

| 항목             | 결과         |
| -------------- | ---------- |
| 명령 실행          | 성공 / 실패    |
| 프로세스 목록        | 확인 / 확인 실패 |
| KDE 관련 프로세스    | 실행 후 기록    |
| Webtop 관련 프로세스 | 실행 후 기록    |
| 오류             | 실행 후 기록    |

프로세스 전체 출력을 README에 복사하지 않는다.

---

## 5. 자원 측정 명령

Windows PowerShell:

```powershell
docker stats web-linux-desktop
```

현재 상태의 값을 기록한 뒤 `Ctrl+C`로 종료한다.

매 측정 상태마다 명령을 새로 실행한다.

---

## 6. KDE 초기 화면 자원 측정

### 상태 조건

```text
KDE Plasma 화면 표시
터미널 종료
내부 브라우저 종료
파일 관리자 종료
오디오 재생 없음
```

Windows PowerShell:

```powershell
docker stats web-linux-desktop
```

### 결과 기록

| 항목                | 결과        |
| ----------------- | --------- |
| 측정 상태             | KDE 초기 화면 |
| Session type      | 실행 후 기록   |
| CPU               | 실행 후 기록   |
| MEM USAGE / LIMIT | 실행 후 기록   |
| MEM %             | 실행 후 기록   |
| 확인 내용             | 화면만 표시    |
| 측정 시각             | 실행 후 기록   |

---

## 7. 터미널 실행 상태 자원 측정

### 상태 조건

```text
KDE 터미널 1개 실행
내부 브라우저 종료
파일 관리자 종료
오디오 재생 없음
```

Windows PowerShell:

```powershell
docker stats web-linux-desktop
```

### 결과 기록

| 항목                | 결과      |
| ----------------- | ------- |
| 측정 상태             | 터미널 실행  |
| Session type      | 실행 후 기록 |
| CPU               | 실행 후 기록 |
| MEM USAGE / LIMIT | 실행 후 기록 |
| MEM %             | 실행 후 기록 |
| 확인 내용             | 터미널 1개  |
| 측정 시각             | 실행 후 기록 |

측정 후 터미널을 종료한다.

---

## 8. 브라우저 실행 상태 자원 측정

### 상태 조건

```text
KDE 터미널 종료
Dolphin 파일 관리자 종료
Webtop 내부 브라우저 1개 실행
웹 페이지 1개 표시
오디오 재생 없음
```

Windows PowerShell:

```powershell
docker stats web-linux-desktop
```

### 결과 기록

| 항목                | 결과       |
| ----------------- | -------- |
| 측정 상태             | 브라우저 실행  |
| Session type      | 실행 후 기록  |
| CPU               | 실행 후 기록  |
| MEM USAGE / LIMIT | 실행 후 기록  |
| MEM %             | 실행 후 기록  |
| 확인 내용             | 웹 페이지 1개 |
| 측정 시각             | 실행 후 기록  |

측정 후 내부 브라우저를 종료한다.

---

## 9. 파일 관리자 실행 상태 자원 측정

### 상태 조건

```text
KDE 터미널 종료
내부 브라우저 종료
Dolphin 파일 관리자 1개 실행
/workspace 표시
오디오 재생 없음
```

Windows PowerShell:

```powershell
docker stats web-linux-desktop
```

### 결과 기록

| 항목                | 결과              |
| ----------------- | --------------- |
| 측정 상태             | 파일 관리자 실행       |
| Session type      | 실행 후 기록         |
| CPU               | 실행 후 기록         |
| MEM USAGE / LIMIT | 실행 후 기록         |
| MEM %             | 실행 후 기록         |
| 확인 내용             | `/workspace` 표시 |
| 측정 시각             | 실행 후 기록         |

측정 후 Dolphin 파일 관리자를 종료한다.

---

## 10. 오디오 재생 상태 자원 측정

### 상태 조건

```text
KDE 터미널 종료
Dolphin 파일 관리자 종료
Webtop 내부 브라우저 1개 실행
오디오가 포함된 콘텐츠 재생
Windows 출력 장치에서 소리 확인
```

Windows PowerShell:

```powershell
docker stats web-linux-desktop
```

### 결과 기록

| 항목                | 결과          |
| ----------------- | ----------- |
| 측정 상태             | 오디오 재생      |
| Session type      | 실행 후 기록     |
| CPU               | 실행 후 기록     |
| MEM USAGE / LIMIT | 실행 후 기록     |
| MEM %             | 실행 후 기록     |
| 확인 내용             | 브라우저 오디오 재생 |
| 측정 시각             | 실행 후 기록     |

---

## 11. 자원 측정 종합 기록

| 측정 상태     | Session type | CPU     | MEM USAGE / LIMIT | MEM %   | 확인 내용           | 측정 시각   |
| --------- | ------------ | ------- | ----------------- | ------- | --------------- | ------- |
| KDE 초기 화면 | 실행 후 기록      | 실행 후 기록 | 실행 후 기록           | 실행 후 기록 | 화면만 표시          | 실행 후 기록 |
| 터미널 실행    | 실행 후 기록      | 실행 후 기록 | 실행 후 기록           | 실행 후 기록 | 터미널 1개          | 실행 후 기록 |
| 브라우저 실행   | 실행 후 기록      | 실행 후 기록 | 실행 후 기록           | 실행 후 기록 | 웹 페이지 1개        | 실행 후 기록 |
| 파일 관리자 실행 | 실행 후 기록      | 실행 후 기록 | 실행 후 기록           | 실행 후 기록 | `/workspace` 표시 | 실행 후 기록 |
| 오디오 재생    | 실행 후 기록      | 실행 후 기록 | 실행 후 기록           | 실행 후 기록 | 브라우저 오디오        | 실행 후 기록 |

### 기록 기준

* 실제 출력값 그대로 기록
* 단위 포함
* 소수점 임의 반올림 금지
* 측정값 보정 금지
* 다른 상태의 값 재사용 금지
* Session type 빈칸 금지
* 측정하지 않은 평균값 작성 금지
* 측정하지 않은 최대값 작성 금지
* 임의 정상 판정 금지
* 임의 부족 판정 금지
* Webtop 공식 요구량으로 표현 금지

---

# 컨테이너 중지 및 재실행

## 1. 컨테이너 중지

Windows PowerShell:

```powershell
docker compose stop
```

상태 확인:

```powershell
docker compose ps
```

### 확인 대상

* `docker compose stop` 오류 없음
* 컨테이너가 실행 상태가 아님
* 브라우저의 기존 KDE 연결 종료

### 결과 기록

| 항목                    | 결과         |
| --------------------- | ---------- |
| `docker compose stop` | 성공 / 실패    |
| 컨테이너 상태               | 실행 후 기록    |
| 브라우저 연결 종료            | 확인 / 확인 실패 |
| 오류                    | 실행 후 기록    |

`docker compose stop`은 컨테이너를 삭제하지 않는다.

Phase 3에서 수행한 `docker compose down` 기반 재생성 검증과 목적이 다르다.

---

## 2. 컨테이너 재실행

Windows PowerShell:

```powershell
docker compose start
```

상태 확인:

```powershell
docker compose ps
```

브라우저 재접속:

```text
https://localhost:3001
```

Webtop KDE 터미널에서 Session type을 다시 확인한다.

```bash
echo "${XDG_SESSION_TYPE:-unknown}"
```

### 확인 대상

* `docker compose start` 오류 없음
* 컨테이너 상태가 `Up`
* HTTPS 포트 연결
* HTTP Basic 인증 가능
* KDE Plasma 화면 표시
* Session type 출력

### 결과 기록

| 항목                     | 결과      |
| ---------------------- | ------- |
| `docker compose start` | 성공 / 실패 |
| 컨테이너 상태                | 실행 후 기록 |
| HTTPS 포트               | 실행 후 기록 |
| HTTPS 재접속              | 성공 / 실패 |
| HTTP Basic 인증          | 성공 / 실패 |
| KDE Plasma 화면          | 성공 / 실패 |
| 재실행 후 Session type     | 실행 후 기록 |
| 오류                     | 실행 후 기록 |

---

# README 최종 정리

## 1. 수정 시점

README는 Phase 4 사용성 검증, 자원 측정, 컨테이너 재실행 검증이 모두 끝난 뒤 수정한다.

검증이 끝나기 전에는 README를 수정하지 않는다.

---

## 2. 반영 범위

README에는 Phase 1~4에서 실제로 확인된 최종 결과를 정리한다.

### Phase 1 결과

* Docker Compose 구성
* `/config` named volume
* `/workspace` bind mount
* HTTPS loopback 포트
* Git 제외 설정

### Phase 2 결과

* 실행 이미지 정보
* 컨테이너 실행 상태
* HTTPS 접속
* HTTP Basic 인증
* KDE Plasma 표시
* 실제 데스크톱 세션 유형

### Phase 3 결과

* Windows·Linux 양방향 파일 공유
* 한글 파일명 공유
* `/config` 영속성
* `/workspace` 유지
* KDE 사용자 설정 유지

### Phase 4 결과

* 화면 해상도
* 전체 화면
* 창 이동과 크기 변경
* 영문 입력
* 한국어 표시
* 한글 입력
* 사용 입력기
* 클립보드 양방향 공유
* 파일 업로드
* 파일 다운로드
* 오디오 출력
* 상태별 CPU 측정값
* 상태별 메모리 측정값
* 컨테이너 stop·start
* 재실행 후 HTTPS 접속

Phase 1~3 문서는 수정하지 않고, 기존 검증 결과를 README에 요약한다.

---

## 3. 작성 기준

* 실제 확인 결과만 작성
* 실패 결과 누락 금지
* 확인하지 않은 항목은 `확인하지 않음`으로 기록
* 실제 사용자명과 비밀번호 기록 금지
* 임의 CPU 권장값 작성 금지
* 임의 메모리 권장값 작성 금지
* 측정하지 않은 평균값 작성 금지
* 측정하지 않은 최대값 작성 금지
* 성능 우수 판정 금지
* 다른 환경에서도 동일하다는 보장 금지
* Phase 4 범위 밖 기능 추가 금지
* 이후 개선 계획 선반영 금지
* README 전체 구조의 불필요한 변경 금지

---

# Git 상태 및 범위 확인

## 1. 검증 전 Git 상태

Windows PowerShell:

```powershell
git status --short
```

Phase 4 시작 시 예상되는 변경:

```text
docs/phase4.md
```

`docs/phase4.md`가 새 파일로 표시되는 것은 Phase 4 정상 범위다.

---

## 2. 검증 파일 Git 제외 확인

Windows PowerShell:

```powershell
git check-ignore -v .\workspace\phase4-download-test.txt
```

파일 업로드 후 bind mount 경로에 생성된 파일도 확인한다.

```powershell
git check-ignore -v .\workspace\phase4-upload-test.txt
```

### 확인 기준

* `workspace/*` 제외 규칙 적용
* Phase 4 검증 파일이 Git 상태에 표시되지 않음

---

## 3. README 수정 후 Git 상태

Windows PowerShell:

```powershell
git status --short
git diff -- README.md
```

### 허용되는 변경

```text
docs/phase4.md
README.md
```

### 변경되면 안 되는 파일

```text
compose.yaml
.env.example
.gitignore
docs/phase1.md
docs/phase2.md
docs/phase3.md
workspace/.gitkeep
```

### 확인 기준

* `docs/phase4.md` 생성
* `README.md` 최종 결과 반영
* Phase 1~3 문서 변경 없음
* Compose 설정 변경 없음
* 실제 인증 정보 미노출
* Phase 4 범위 밖 신규 파일 없음

---

# 실행 결과 기록

## 1. 실행 환경

| 항목           | 결과                       |
| ------------ | ------------------------ |
| 확인 날짜        | 실행 후 기록                  |
| 호스트 운영체제     | Windows 11               |
| 컨테이너 실행 환경   | Docker Desktop · WSL 2   |
| 컨테이너         | `web-linux-desktop`      |
| 데스크톱         | KDE Plasma               |
| 접속 주소        | `https://localhost:3001` |
| Session type | 실행 후 기록                  |
| 화면 해상도       | 실행 후 기록                  |

---

## 2. 사용성 결과

| 확인 항목                 | 결과      | 비고      |
| --------------------- | ------- | ------- |
| 화면 해상도                | 실행 후 기록 | 실행 후 기록 |
| 전체 화면 진입              | 실행 후 기록 | 실행 후 기록 |
| 전체 화면 해제              | 실행 후 기록 | 실행 후 기록 |
| 창 이동                  | 실행 후 기록 | 실행 후 기록 |
| 창 크기 변경               | 실행 후 기록 | 실행 후 기록 |
| 영문 입력                 | 실행 후 기록 | 실행 후 기록 |
| 한국어 표시                | 실행 후 기록 | 실행 후 기록 |
| 한글 파일명                | 실행 후 기록 | 실행 후 기록 |
| 한글 입력                 | 실행 후 기록 | 실행 후 기록 |
| 사용 입력기                | 실행 후 기록 | 실행 후 기록 |
| Windows → Webtop 클립보드 | 실행 후 기록 | 실행 후 기록 |
| Webtop → Windows 클립보드 | 실행 후 기록 | 실행 후 기록 |
| 파일 업로드                | 실행 후 기록 | 실행 후 기록 |
| 파일 다운로드               | 실행 후 기록 | 실행 후 기록 |
| 오디오 출력                | 실행 후 기록 | 실행 후 기록 |

---

## 3. 컨테이너 정보

| 확인 항목            | 결과      |
| ---------------- | ------- |
| 컨테이너 상태          | 실행 후 기록 |
| RestartCount     | 실행 후 기록 |
| 실행 이미지           | 실행 후 기록 |
| `/config` 마운트    | 실행 후 기록 |
| `/workspace` 마운트 | 실행 후 기록 |
| `docker top`     | 실행 후 기록 |
| KDE 관련 프로세스      | 실행 후 기록 |
| Webtop 관련 프로세스   | 실행 후 기록 |

---

## 4. 자원 측정 결과

| 측정 상태     | Session type | CPU     | MEM USAGE / LIMIT | MEM %   | 확인 내용           | 측정 시각   |
| --------- | ------------ | ------- | ----------------- | ------- | --------------- | ------- |
| KDE 초기 화면 | 실행 후 기록      | 실행 후 기록 | 실행 후 기록           | 실행 후 기록 | 화면만 표시          | 실행 후 기록 |
| 터미널 실행    | 실행 후 기록      | 실행 후 기록 | 실행 후 기록           | 실행 후 기록 | 터미널 1개          | 실행 후 기록 |
| 브라우저 실행   | 실행 후 기록      | 실행 후 기록 | 실행 후 기록           | 실행 후 기록 | 웹 페이지 1개        | 실행 후 기록 |
| 파일 관리자 실행 | 실행 후 기록      | 실행 후 기록 | 실행 후 기록           | 실행 후 기록 | `/workspace` 표시 | 실행 후 기록 |
| 오디오 재생    | 실행 후 기록      | 실행 후 기록 | 실행 후 기록           | 실행 후 기록 | 브라우저 오디오        | 실행 후 기록 |

---

## 5. 컨테이너 중지 및 재실행 결과

| 확인 항목                  | 결과      |
| ---------------------- | ------- |
| `docker compose stop`  | 실행 후 기록 |
| 중지 상태                  | 실행 후 기록 |
| 브라우저 연결 종료             | 실행 후 기록 |
| `docker compose start` | 실행 후 기록 |
| 재실행 상태                 | 실행 후 기록 |
| HTTPS 포트               | 실행 후 기록 |
| HTTPS 재접속              | 실행 후 기록 |
| HTTP Basic 인증          | 실행 후 기록 |
| KDE Plasma 화면          | 실행 후 기록 |
| 재실행 후 Session type     | 실행 후 기록 |

---

## 6. 변경 파일

```text
작업 완료 후 기록
```

허용되는 변경:

```text
docs/phase4.md
README.md
```

---

## 7. 확인하지 못한 항목

```text
작업 완료 후 기록
```

확인하지 못한 항목이 없으면:

```text
없음
```

---

## 8. 발생한 문제

```text
실제 오류, 발생 조건, 검증에 미친 영향 기록
```

문제가 없으면:

```text
확인된 문제 없음
```

---

## 9. 최종 상태

```text
Phase 4 실행 후 실제 결과 기록
```

---

## 5. 주의사항

* Phase 1~3 완료 상태에서 Phase 4 시작
* `docs/phase1.md` 수정 금지
* `docs/phase2.md` 수정 금지
* `docs/phase3.md` 수정 금지
* 실제 사용자명과 비밀번호 기록 금지
* `.env` 열람 및 출력 금지
* `docker inspect` 전체 JSON 기록 금지
* 화면 해상도 추정 금지
* Session type 추정 금지
* Phase 2의 Session type을 Phase 4 결과로 복사하지 않음
* 한글 파일명 표시를 한글 입력 성공으로 처리하지 않음
* 클립보드 방향별 결과 구분
* 파일 업로드와 bind mount 공유 구분
* 업로드 원본을 `workspace` 외부에 준비
* 다운로드 원본과 Windows 다운로드 결과 구분
* 오디오 재생과 Windows 출력 확인 구분
* 상태별 측정 대상 외 애플리케이션 종료
* 다른 상태의 자원 측정값 재사용 금지
* 임의 CPU 기준 작성 금지
* 임의 메모리 기준 작성 금지
* 측정하지 않은 평균값 작성 금지
* 측정하지 않은 최대값 작성 금지
* 컨테이너 재시작과 재생성 혼동 금지
* Phase 4에서는 `docker compose down` 실행 불필요
* Phase 4에서는 Docker volume 삭제 명령 실행 금지
* 기존 Compose 설정 변경 금지
* 권한 오류 발생 시 `privileged` 추가 금지
* 권한 오류 발생 시 `seccomp:unconfined` 추가 금지
* 한글 입력 실패 시 패키지 설치 금지
* 모든 검증 완료 전 README 수정 금지
* 실제 결과 확인 후 README만 최종 정리

---

## 6. 정상 기준

### 사용성

* 브라우저에서 KDE Plasma 조작 가능
* 실제 Wayland 또는 X11 Session type 기록
* 화면 해상도 기록
* 전체 화면 진입 가능
* 전체 화면 해제 가능
* 창 이동 가능
* 창 크기 변경 가능
* 영문 입력 가능
* 한국어 표시 결과 기록
* 한글 파일명 표시 결과 기록
* 한글 입력 결과 기록
* 사용 입력기 기록

### 브라우저 연동 기능

* Windows → Webtop 클립보드 결과 기록
* Webtop → Windows 클립보드 결과 기록
* Webtop 파일 업로드 결과 기록
* Webtop 파일 다운로드 결과 기록
* Windows 오디오 출력 결과 기록

### 자원 측정

* 컨테이너 상태 확인
* 컨테이너 RestartCount 확인
* 컨테이너 프로세스 확인
* KDE 초기 화면 CPU와 메모리 기록
* 터미널 실행 상태 CPU와 메모리 기록
* 브라우저 실행 상태 CPU와 메모리 기록
* 파일 관리자 실행 상태 CPU와 메모리 기록
* 오디오 재생 상태 CPU와 메모리 기록
* 모든 자원 측정값과 Session type 연결
* 실제 측정값만 기록
* 임의 성능 기준 미작성

### 컨테이너 재실행

* 컨테이너 중지 성공
* 중지 상태 확인
* 컨테이너 재실행 성공
* 컨테이너 상태 `Up`
* HTTPS 재접속 가능
* HTTP Basic 인증 가능
* KDE Plasma 화면 표시
* 재실행 후 Session type 기록

### 프로젝트 범위

* `docs/phase4.md` 생성
* README에 실제 결과 반영
* Phase 1~3 문서 변경 없음
* Compose 설정 변경 없음
* 실제 인증 정보 미노출
* 검증 파일 Git 제외
* Phase 4 범위 밖 파일과 기능 없음

---

## 7. 완료 기준

* [ ] `docs/phase4.md` 작성
* [ ] Webtop 컨테이너 실행 상태 확인
* [ ] 실제 데스크톱 Session type 기록
* [ ] 화면 해상도 확인
* [ ] 전체 화면 진입 확인
* [ ] 전체 화면 해제 확인
* [ ] Wayland 또는 X11 환경에서 창 이동 확인
* [ ] Wayland 또는 X11 환경에서 창 크기 변경 확인
* [ ] 영문 입력 확인
* [ ] 한국어 표시 결과 기록
* [ ] 한글 파일명 결과 기록
* [ ] 한글 입력 결과 기록
* [ ] 사용 입력기 기록
* [ ] Windows → Webtop 클립보드 결과 기록
* [ ] Webtop → Windows 클립보드 결과 기록
* [ ] 업로드 전 대상 파일 미존재 확인
* [ ] 파일 업로드 결과 기록
* [ ] Windows 다운로드 폴더에서 파일 다운로드 확인
* [ ] 파일 다운로드 결과 기록
* [ ] 오디오 출력 결과 기록
* [ ] 형식화된 `docker inspect` 실행
* [ ] 컨테이너 상태 기록
* [ ] 컨테이너 RestartCount 기록
* [ ] `/config` 마운트 기록
* [ ] `/workspace` 마운트 기록
* [ ] `docker top web-linux-desktop` 실행
* [ ] KDE 관련 프로세스 확인
* [ ] Webtop 관련 프로세스 확인
* [ ] KDE 초기 화면 CPU 사용량 기록
* [ ] KDE 초기 화면 메모리 사용량 기록
* [ ] 터미널 실행 상태 CPU 사용량 기록
* [ ] 터미널 실행 상태 메모리 사용량 기록
* [ ] 브라우저 실행 상태 CPU 사용량 기록
* [ ] 브라우저 실행 상태 메모리 사용량 기록
* [ ] 파일 관리자 실행 상태 CPU 사용량 기록
* [ ] 파일 관리자 실행 상태 메모리 사용량 기록
* [ ] 오디오 재생 상태 CPU 사용량 기록
* [ ] 오디오 재생 상태 메모리 사용량 기록
* [ ] 모든 측정 결과와 Session type 연결
* [ ] 각 측정 상태에서 대상 외 애플리케이션 종료
* [ ] 컨테이너 중지 확인
* [ ] 컨테이너 재실행 확인
* [ ] 재실행 후 HTTPS 재접속 확인
* [ ] 재실행 후 HTTP Basic 인증 확인
* [ ] 재실행 후 KDE Plasma 화면 확인
* [ ] 재실행 후 Session type 기록
* [ ] 검증용 `/workspace` 파일 Git 제외 확인
* [ ] README 최종 정리
* [ ] `docs/phase1.md` 미수정
* [ ] `docs/phase2.md` 미수정
* [ ] `docs/phase3.md` 미수정
* [ ] Compose 설정 미수정
* [ ] 실제 인증 정보 미노출
* [ ] 임의 성능 기준 미작성
* [ ] 실제 측정값만 기록
* [ ] Phase 4 범위 밖 파일과 기능 없음
