# chagok-app-policy

차곡차곡 앱의 이용약관·개인정보 처리방침을 Netlify로 게시한다(`publish` 브랜치 → `https://chagok-app-terms.netlify.app`).
앱은 `https://chagok-app-terms.netlify.app/{terms,privacy}/`를 앱 안 시트로 열고,
로그인한 사용자의 재동의 판단에 `versions.json`을 읽는다.

## 개정하는 방법

**정식 출시 전에는 새 판을 만들지 않고 1판(`terms/v1`, `privacy/v1`)을 고친다** (2026-10-14 결정). 아래는 출시 뒤 개정할 때다.

1. `terms/v2/index.html`처럼 새 판 폴더를 만든다. 이미 시행된 판의 본문은 고치지 않는다(오탈자만).
2. `versions.json`에 판을 추가한다. `postedAt`(게시일)과 `effectiveAt`(시행일)은 로컬 날짜 `YYYY-MM-DD`.
   - 사전 고지: 시행일 7일 전, 이용자에게 불리하면 30일 전에 게시한다.
   - `requiresConsent`: 로그인한 사용자에게 다시 동의를 받을 개정이면 `true`.
3. 시행일 전까지 `terms/`는 이전 판을 보여주고 위에 "M월 D일부터 바뀌어요"를 띄운다.
   판 선택 규칙은 앱의 `src/policy/versions.ts`와 같다.

## 게시 전 채울 것

판 날짜, 약관 끝과 첫 화면의 사업장 주소, 전화번호, 통신판매업 신고번호(유료 상품을 팔기 전에, 전자상거래법 제10조와 제13조). 최종 문안은 법률 검토를 받는다.

## 게시

Netlify는 `publish` 브랜치만 배포한다. `main`을 `publish`에 합치고 push하면 게시된다.

2026-09-30부터 초안을 공개해 두었다(앱 연결 확인용). `_headers`가 모든 페이지에 `X-Robots-Tag: noindex`를 붙여 검색에는 걸리지 않는다.
공식 게시 때: 위 「게시 전 채울 것」을 채우고, 판 날짜(`versions.json`, 각 판 상단)를 게시하는 날로 맞추고, `_headers`를 지운다.
