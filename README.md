# DRTV 원툴

매체사 큐시트를 DTV 납품용 Planned Cue sheet 로우로 변환하고, 플랜·실송출 로우를 월 단위로 누적 관리하는 사내 툴입니다.
GitHub Pages(정적 호스팅) + Firebase(로그인, Firestore)로 동작합니다.

## 파일
| 파일 | 내용 |
|---|---|
| index.html | 앱 전체 (변환 엔진 포함, 단일 파일) |
| firebase-config.js | Firebase 웹 앱 설정값 (직접 입력) |
| firestore.rules | Firestore 보안 규칙 (콘솔에 붙여넣기) |
| .nojekyll | GitHub Pages 처리용 빈 파일 |

브랜드 프로필 파일(brand_presets.json)과 디멘션·큐시트 등 브랜드사 자료는 이 저장소에 올리지 않습니다.

## 1. Firebase 준비 (최초 1회)
1. Firebase 콘솔에서 프로젝트를 만듭니다. 기존 사내 툴처럼 툴별로 분리하는 것을 권장합니다 (예: drtv-onetool).
2. Authentication > 로그인 방법 > 이메일/비밀번호 사용 설정 > "이메일 링크(비밀번호 없는 로그인)" 사용 설정
3. Authentication > 설정 > 승인된 도메인에 저장소 계정 주소(예: `metahong.github.io` 또는 `spgnflwr.github.io`) 추가
4. Firestore Database 만들기 (리전: asia-northeast3 서울 권장) > 규칙 탭에 `firestore.rules` 내용을 붙여넣고 게시
5. 프로젝트 설정 > 일반 > 내 앱 > 웹 앱 추가 > 표시되는 설정값을 `firebase-config.js` 에 입력

## 2. GitHub Pages 배포
1. spgnflwr 계정에 저장소를 만듭니다 (예: drtv-onetool).
2. 이 폴더의 파일 4개를 올립니다.
   ```
   git clone https://github.com/spgnflwr/drtv-onetool.git
   cp index.html firebase-config.js firestore.rules .nojekyll README.md drtv-onetool/
   cd drtv-onetool && git add . && git commit -m "DRTV 원툴 v1.5" && git push
   ```
3. Settings > Pages > Branch: main / (root) > Save
4. 주소: `https://metahong.github.io/drtv-onetool/ (또는 저장소 계정 주소)`

## 3. 처음 쓸 때
1. 회사 메일(@springnflower.com)을 넣고 로그인 링크를 받아, 같은 브라우저에서 링크를 누릅니다.
2. 브랜드 설정 > "프로필 파일 가져오기"로 brand_presets.json 을 넣습니다 (사내 DB에만 저장됨).
3. 브랜드를 추가하면서 프로필을 고릅니다.
4. 디멘션 > dimension.xlsx 와 브랜드 타겟의 평균IMPS 를 넣습니다.
5. 큐시트 변환 > 매체사 큐시트를 넣고 변환 > DB에 버전 저장 > 큐시트 DB에서 확정
   - 확정하면 플랜 DB의 그 달이 월 전체 교체(기존 삭제 후 신규 추가)됩니다.
6. 시연판(claude.ai)에 넣어 둔 자료는 시연판 큐시트 DB > 전체 백업 > 사내판 큐시트 DB > 백업에서 복원으로 옮깁니다.

## 데이터 위치
Firestore `data/team/springnflower/ws/...` 아래에 문서 단위로 저장합니다.
| 경로 | 내용 |
|---|---|
| ws | 브랜드 목록·설정·프로필 |
| ws/dims/{brand} | 디멘션 (채널·소재 별칭, 클락매트릭스, 미디어플랜, 납품명) |
| ws/imps_{brand}/* | 브랜드별 평균IMPS |
| ws/uploads/{id}, ws/spots/* | 큐시트 버전과 변환 로우 |
| ws/files/* | 업로드한 매체사 큐시트 원본 |
| ws/facts/{kind_brand_yymm}, ws/factrows/* | 플랜·실송출 DB (월 단위) |

## 알려진 한계
- 브랜드별 담당자 제한은 화면 표시만 거릅니다. 보안 규칙은 회사 메일 로그인 여부까지만 검사하므로, 로그인한 직원은 모든 브랜드 자료를 읽을 수 있습니다.
- 실송출 변환(스팟 단위 판정)은 변환 코드를 받은 뒤 연결합니다. 지금은 Actual Cue sheet 가져오기와 채널 합계 비교까지입니다.
- IPTV 행은 자동 생성하지 않습니다.
