# Work-Reports

LMS, 챗봇 등 프로젝트 작업 결과를 팀원에게 공유하기 위한 공개 HTML 보고서 저장소입니다.

설계·결정의 원본 문서는 각 프로젝트 문서 저장소에서 관리하고, 이 저장소에는 브라우저에서 바로 열람할 수 있는 공유용 보고서만 게시합니다.

## 배포 주소

- 보고서 목록: <https://junseok-dev.github.io/Work-Reports/>
- 첫 보고서: [LMS-AI 수강역량증명서 실데이터 흐름 및 필요 데이터](https://junseok-dev.github.io/Work-Reports/reports/lms/2026-08-07_lms-ai_수강역량증명서_실데이터_흐름_및_필요_데이터/)

> 최초 배포 전에는 GitHub 저장소의 `Settings → Pages → Build and deployment → Source`를 `GitHub Actions`로 선택해야 합니다.

## 운영 원칙

- 저장소와 배포 사이트는 공개 상태입니다. 개인정보, 인증정보, 운영 DB 원문, 비공개 URL은 올리지 않습니다.
- 완성된 보고서는 `reports/<프로젝트>/<보고서>/` 단위로 관리합니다.
- 보고서 본문은 `sections/`에서 수정하고 `index.html`은 `build.py`로 생성합니다.
- GitHub Pages에는 저장소 루트가 아니라 `dist/` 결과물만 배포합니다.
- 검색 노출을 줄이기 위해 `robots.txt`와 `noindex`를 사용하지만, 이는 접근 권한 통제가 아닙니다.

## 구조

```text
Work-Reports/
├─ .github/workflows/pages.yml       main push 시 자동 배포
├─ assets/                           보고서 목록용 공통 자산
├─ reports/
│  ├─ lms/
│  │  └─ <보고서>/
│  │     ├─ sections/                본문 원본 조각
│  │     │  ├─ 00-shell.html         문서 head·header·footer
│  │     │  └─ 01-*.html             번호 순서대로 조립할 본문
│  │     └─ index.html               build.py 생성 결과
│  └─ chatbot/                       챗봇 보고서 추가 위치
├─ tools/import_report.py            기존 단일 HTML을 sections로 변환
├─ build.py                          보고서 조립 및 dist 생성
├─ index.html                        전체 보고서 목록
├─ robots.txt
└─ dist/                             배포 결과물, Git 미추적
```

`LMS-REPORT`의 보고서별 `sections/`, 공통 자산, Python 조립기, 자동 배포 흐름을 유지했습니다. 여러 프로젝트를 한 저장소에서 구분하기 위해 `reports/lms`, `reports/chatbot` 단계를 추가했고, 공개 범위를 안전하게 제한하기 위해 배포 산출물은 `dist/`로 분리했습니다.

## 로컬 확인

```powershell
python build.py
python tools/check_site.py
python -m http.server 8000 --directory dist
```

브라우저에서 <http://127.0.0.1:8000/>을 엽니다.

특정 보고서의 조립만 확인하려면 보고서 폴더의 상대 경로를 전달합니다.

```powershell
python build.py "reports/lms/2026-08-07_lms-ai_수강역량증명서_실데이터_흐름_및_필요_데이터"
```

## 기존 HTML 보고서 추가

완성된 단일 HTML을 `LMS-REPORT` 형식의 조각 파일로 변환할 수 있습니다.

```powershell
python tools/import_report.py `
  "C:\path\to\report.html" `
  "reports/lms/2026-08-07_report-name"

python build.py
```

변환기는 `<main class="content">` 안의 최상위 `<section>`을 제목 번호 순서로 나누고, 나머지 문서 구조를 `00-shell.html`에 보존합니다. 변환 후에는 루트 `index.html`에 보고서 카드를 추가합니다.

## 자동 배포

`main`에 반영되면 `.github/workflows/pages.yml`이 다음 순서로 실행됩니다.

1. `python3 build.py`로 모든 보고서를 조립합니다.
2. 공개 가능한 파일만 `dist/`에 모읍니다.
3. `dist/`만 GitHub Pages 아티팩트로 업로드합니다.
4. 최신 성공 빌드를 공개 주소에 배포합니다.

별도의 Firebase 프로젝트나 서비스 계정 키는 사용하지 않습니다.
