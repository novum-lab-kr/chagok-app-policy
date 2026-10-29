# chagok-app-policy

차곡차곡 앱의 소개(랜딩)·고객 지원·이용약관·개인정보 처리방침 사이트. `https://chagok.novum-lab.com` 에 게시한다(`main` 브랜치 → 운영 VPS).
앱은 `https://chagok.novum-lab.com/{terms,privacy}/`를 앱 안 시트로 열고, 판 정보는 `versions.json`을 읽는다.

| 경로 | 내용 | 검색 노출 |
|---|---|---|
| `/` | 랜딩(`index.html`, 스타일 `assets/landing.css`, 그림 `assets/img`, 영상 `assets/vid`, 글꼴 `assets/fonts` — Pretendard, Neo둥근모 원본 그대로, SIL OFL 1.1, 폴더마다 `LICENSE.txt`) | 허용 |
| `/support/` | 문의, 자주 묻는 것, 운영자 정보 | 차단(공식 게시 전) |
| `/terms/`, `/privacy/`, `/delete-account/`, `/versions.json` | 약관, 처리방침, 계정 삭제 안내, 판 목록 — **앱이 읽는 경로라 바꾸지 않는다** | 차단(공식 게시 전) |

랜딩의 App Store 버튼은 출시 전이라 누를 수 없는 「곧 출시」다. 주소가 생기면 `index.html` 맨 위 주석대로 세 곳을 바꾼다.
판 본문(`terms/v1` 등)의 사이트 안 링크는 `/delete-account/`처럼 **사이트 기준 절대 경로**로 쓴다 — 같은 본문이 `/privacy/v1/`과 `/privacy/`(끼워 넣기) 두 주소에서 보여 상대 경로는 한쪽에서 깨진다.

## 개정하는 방법

**정식 출시 전에는 새 판을 만들지 않고 1판(`terms/v1`, `privacy/v1`)을 고친다** (2026-10-14 결정). 아래는 출시 뒤 개정할 때다.

1. `terms/v2/index.html`처럼 새 판 폴더를 만든다. 이미 시행된 판의 본문은 고치지 않는다(오탈자만).
2. `versions.json`에 판을 추가한다. `postedAt`(게시일)과 `effectiveAt`(시행일)은 로컬 날짜 `YYYY-MM-DD`.
   - 사전 고지: 시행일 7일 전, 이용자에게 불리하면 30일 전에 게시한다.
   - `requiresConsent`: 로그인한 사용자에게 다시 동의를 받을 개정이면 `true`.
3. 시행일 전까지 `terms/`는 이전 판을 보여주고 위에 "M월 D일부터 바뀌어요"를 띄운다.
   판 선택 규칙은 앱의 `src/policy/versions.ts`와 같다.

## 게시 전 채울 것

판 날짜, 약관 끝과 지원 페이지(`support/`)의 사업장 주소, 전화번호, 통신판매업 신고번호(유료 상품을 팔기 전에, 전자상거래법 제10조와 제13조). 최종 문안은 법률 검토를 받는다.

## 게시

`main`에 합치면(push) GitHub Actions(`.github/workflows/deploy.yml`)가 rsync로 VPS 배포 폴더에 올린다. **`main`에 합치는 순간 운영 반영**이다. `README.md`, `.github`, `_headers`, `.nojekyll`은 올리지 않는다.
서버 위치: VPS `/var/www/chagok-site`(배포 계정은 rrsync 로 이 폴더에 묶여 있어 `DEPLOY_PATH`는 `/`), Caddy 블록 `chagok.novum-lab.com`, 검색 차단은 그 블록의 `@noindex` 두 줄.
서버 주소, 사용자, 폴더, SSH 키, 호스트 키는 저장소 secret(`DEPLOY_HOST`, `DEPLOY_USER`, `DEPLOY_PATH`, `DEPLOY_SSH_KEY`, `DEPLOY_KNOWN_HOSTS`)에만 둔다.

2026-09-30부터 초안을 공개해 두었다(앱 연결 확인용). 검색 차단은 서버 Caddy 블록이 `/` 를 뺀 모든 경로에 `X-Robots-Tag: noindex` 헤더를 붙여 한다(레포에는 없다).
공식 게시 때: 위 「게시 전 채울 것」을 채우고, 판 날짜(`versions.json`, 각 판 상단)를 게시하는 날로 맞추고, Caddy 블록의 noindex 규칙을 지운다.

(2026-10 이전에는 Netlify가 `publish` 브랜치를 `https://chagok-app-terms.netlify.app`에 배포했고 `_headers`로 검색을 막았다. VPS로 옮기면서 내린다.)
