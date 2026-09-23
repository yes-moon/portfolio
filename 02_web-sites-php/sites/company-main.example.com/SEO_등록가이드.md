# [회사명] 검색 노출 등록 가이드 (연구소 우선)

> **목표**: 외부에서 "[회사명]" 검색 시 **[주소] 빅데이터·AI 연구소** 정보가 먼저 노출되도록 한다.
> **작성일**: 2026-05-15
> **사이트 내부 작업 (완료)**: JSON-LD(Organization + LocalBusiness, R&D 우선), 메타태그(geo/og), sitemap.xml, robots.txt 모두 적용.
> **남은 작업**: 아래 ① ~ ⑤ 외부 서비스에 사장님 명의 계정으로 직접 등록 (로그인 + 본인확인 필요해 대리 등록 불가).

---

## 0. 모든 등록처에 공통으로 쓰는 NAP 시트 (복사용)

> **NAP = Name·Address·Phone** — 검색엔진은 여러 사이트의 NAP가 **글자 단위로 일치**하면 동일 업체로 간주하고 신뢰 점수를 올립니다. **반드시 동일 표기**로 등록.

### 한글
| 항목 | 값 |
|---|---|
| 업체명 (대표) | **[회사명] 빅데이터·AI 연구소** |
| 업체명 (모회사) | [회사명] 주식회사 |
| 대표 주소 (연구소·우선) | [주소] XX층 XXXX호 |
| 보조 주소 (본사) | [주소] X층 |
| 대표전화 | 0XX-XXXX-XXXX |
| 국제표기 | +82-2-XXXX-XXXX |
| 이메일 | user@example.com |
| 홈페이지 | https://company-main.example.com |
| 업종 | 빅데이터·머신러닝·인공지능(AI) 알고리즘 컨설팅·연구 |
| 운영시간 | 평일 09:00 ~ 18:00 (주말·공휴일 휴무) |

### 영문 (Google·LinkedIn·해외용)
| Field | Value |
|---|---|
| Business name | **Company Big Data & AI R&D Center** |
| Parent | Company Co., Ltd. |
| Primary address | Unit XXX, XXF, [Building], [Address], [Address], Seoul, Republic of Korea |
| Secondary address (HQ) | XF, [Building], [Address], [Address], Seoul, Republic of Korea |
| Phone | +82-2-XXXX-XXXX |
| Email | user@example.com |
| Website | https://company-main.example.com |
| Category | Big Data Consulting · Machine Learning · AI Research |
| Hours | Mon–Fri 09:00–18:00 KST |

### 회사 소개 단문 (공통 사용)
> [회사명] 주식회사는 빅데이터·머신러닝·인공지능(AI) 알고리즘 전문기업입니다. [주소], [지역] 본사와 연계해 공공·민간 데이터 분석과 AI 컨설팅을 수행합니다.

영문:
> Company Co., Ltd. is a big-data, machine-learning and AI consulting firm. Its primary R&D facility — the **Company Big Data & AI R&D Center** — is located at [Building], [Address], [Address], Seoul, with the headquarters office in [Address], Seoul.

---

## ① Google Search Console + Google 비즈니스 프로필 ★ 최우선

### A. Search Console (사이트맵 등록 — 무료, 즉시)
1. https://search.google.com/search-console 접속 → 사장님 Google 계정으로 로그인
2. "속성 추가" → **URL 접두어**: `https://company-main.example.com/` 입력
3. 소유권 확인 방법 4가지 중 **HTML 태그**를 선택, 안내된 `<meta name="google-site-verification" content="...">` 한 줄을 복사
4. 그 메타태그 한 줄을 카톡·메일로 보내주시면 → 제가 `app/views/layouts/main.php` head에 즉시 삽입·재배포
5. Search Console 화면에서 "확인"
6. "사이트맵" 메뉴 → `sitemap.xml` 입력 → 제출
7. "URL 검사"에 `https://company-main.example.com/` 입력 → "색인 생성 요청"

### B. Google 비즈니스 프로필 (구 Google My Business) — **연구소를 "기본 위치"로 등록**
1. https://business.google.com/ 접속 → Google 계정 로그인
2. "비즈니스 추가" → 한 번에 하나
3. **첫 번째로 연구소부터 등록** (검색 노출 우선순위가 됩니다):
   - 비즈니스 이름: `[회사명] 빅데이터·AI 연구소`
   - 카테고리: `정보 기술 회사` 또는 `경영 컨설팅` (가장 적합한 것)
   - 위치 추가 → 위 한글 연구소 주소 입력
   - 우편번호 검색 시 "[지역구] [주소]" 입력
   - 전화·웹사이트·운영시간: 위 시트대로
   - 본인 확인: 우편엽서(2~4주) / 전화 / 영상 통화 중 선택
4. **두 번째로 본사 추가**:
   - 비즈니스 이름: `[회사명] 주식회사` (또는 `[회사명] 주식회사 (본사)`)
   - 같은 절차, 본사 주소 입력
5. 두 위치 모두 등록되면 Knowledge Panel에 "본사 외 1개 위치" 식으로 나옵니다. 검색어가 "[회사명] + [주소]/연구소"면 연구소가, "[회사명] + [지역]/본사"면 본사가 더 위에 나오게 됩니다.

**팁**: 본인확인 우편엽서가 연구소 주소로 가야 하니, 연구소에 수령 가능한 직원을 미리 지정해 두세요.

---

## ② 네이버 (한국 검색 점유율 1위, 가장 중요) ★ 최우선

### A. 네이버 서치어드바이저 (사이트맵 등록 — 무료, 즉시)
1. https://searchadvisor.naver.com/ 접속 → 사장님 네이버 ID 로그인
2. "웹마스터 도구" → "사이트 등록" → `https://company-main.example.com` 입력
3. 소유권 확인 → **HTML 태그** 방식 선택 → 메타태그 받기
4. 받은 `<meta name="naver-site-verification" content="...">` 한 줄도 카톡·메일로 → 제가 사이트에 삽입·재배포
5. 확인 완료되면 "요청 → 사이트맵 제출" → `sitemap.xml` 입력
6. "요청 → 웹페이지 수집"에 `/`, `/news`, `/contact` 각각 수집 요청

### B. 네이버 플레이스 (지도/스마트플레이스 등록) — **연구소 우선**
1. https://smartplace.naver.com/ 접속 → 네이버 ID 로그인
2. "신규 등록" → "업체 등록"
3. **첫 번째로 연구소 등록**:
   - 업체명: `[회사명] 빅데이터·AI 연구소`
   - 업종: `IT/소프트웨어` → `소프트웨어개발` 또는 `컨설팅`
   - 주소: 위 한글 연구소 주소
   - 사업자등록증 사본 업로드 (PDF/JPG)
   - 대표번호, 운영시간, 홈페이지 입력
   - 대표 사진 1장 이상 (연구소 외관 또는 [주소] 빌딩 사진 권장)
4. **두 번째로 본사 등록** (같은 절차)
5. 심사 1~3 영업일. 통과 후 네이버 지도/검색에 노출

### C. 네이버 모두 (modoo.at) — 사업자등록 정보 검색 보조
- 선택사항. 무료 회사 홈페이지 만들면 "[회사명]" 검색 시 사이드 카드에 잡힙니다.

---

## ③ 카카오 (카카오맵·다음 검색)

### A. 카카오맵 장소 등록 — **연구소·본사 각각**
1. https://map.kakao.com/ → 우상단 "내 가게 등록" (PC 기준)
2. 카카오 계정 로그인
3. "신규 장소 등록" → 위 한글 NAP 입력 (연구소 먼저)
4. 사업자등록증, 명함, 간판 등 인증자료 업로드
5. 카카오 측 검토 (보통 3~7일)

### B. 다음 검색 등록
- 카카오맵에 장소가 등록되면 다음(Daum) 검색 결과의 사이드 카드와 지도 영역에 자동으로 반영됩니다. 별도 등록 불필요.

### C. 카카오 채널 (선택)
- 카카오톡 채널 만들면 "[회사명]" 카카오 검색에 채널 카드가 노출됩니다. https://center-pf.kakao.com/

---

## ④ Bing Webmaster + LinkedIn

### A. Bing Webmaster Tools (해외 검색·ChatGPT 검색 소스)
1. https://www.bing.com/webmasters → Microsoft 계정 로그인
2. "사이트 추가" → `https://company-main.example.com` 입력
3. **Google Search Console과 연결 옵션이 있음** — 클릭 한 번으로 Search Console에서 확인된 속성·사이트맵을 가져옵니다. 따로 메타태그 안 받아도 됩니다.
4. 사이트맵 자동 동기화 확인

### B. LinkedIn 회사 페이지 (영문 검색·해외 BD에 효과)
1. https://www.linkedin.com/company/setup/new/
2. 회사명: `Company Co., Ltd.`
3. 본사 주소: 위 영문 **연구소** 주소를 우선 입력 (해외 검색 노출 목적)
4. 회사 소개: 위 영문 단문 그대로
5. 로고·배너 업로드

---

## ⑤ 사업자등록·공공 디렉토리 (자동·수동 혼합)

### A. 국세청 사업자등록 주소 변경 (법적 의무)
- 본사 주소가 변경되면 **변경일로부터 20일 이내**에 사업자등록 정정신고 필요 (홈택스 또는 세무서 방문)
- 연구소는 사업장 추가 신고 (해당 시)
- 변경 신고 후 1~2주 내 사업자정보 공개 사이트(NICE신용정보·이크레딧 등)에 자동 반영

### B. 한국기업데이터·NICE·잡코리아 등 자동 디렉토리
- 사업자등록 주소가 정정되면 위 기관 데이터에 자동 반영되어 검색 결과에도 흘러갑니다. 사장님이 직접 등록할 필요 없음.

### C. 명함·소개서·계약서 (수시)
- 모든 마케팅 자료에 새 주소(연구소 우선 + 본사 병기)를 사용. 외부에 노출되는 모든 PDF·이미지에 새 주소가 들어가야 검색엔진의 NAP 인용(Citation) 점수가 올라갑니다.

---

## ⑥ 등록 후 검증 (제가 자동화 가능)

사장님이 위 절차를 1~3주 안에 진행하시는 동안, 다음은 제가 주기적으로 돌려드릴 수 있습니다.

| 검증 항목 | 방법 | 주기 |
|---|---|---|
| JSON-LD 문법 | Google Rich Results Test (https://search.google.com/test/rich-results?url=https://company-main.example.com) | 1회 |
| 사이트맵 응답 | `curl https://company-main.example.com/sitemap.xml` | 1회 (완료) |
| robots.txt 응답 | `curl https://company-main.example.com/robots.txt` | 1회 (완료) |
| 색인 상태 | `site:company-main.example.com` Google·네이버 검색 | 주 1회 |
| 지식 패널 노출 | "[회사명]" Google 검색 → 우측 카드 | 등록 후 2~4주 |

원하시면 "검증 돌려줘"라고만 말씀하시면 위 항목을 자동으로 점검해 보고서로 드리겠습니다.

---

## 등록 순서 추천 (시간순)

1. **오늘**: ② 네이버 서치어드바이저 사이트 등록 → 메타태그 받아 카톡 → 제가 즉시 삽입·재배포
2. **오늘**: ① Google Search Console 등록 → 메타태그 받아 카톡 → 제가 즉시 삽입·재배포
3. **이번 주**: ② 네이버 플레이스 연구소 등록 (사업자등록증 사본 준비) → 본사 추가
4. **이번 주**: ① Google 비즈니스 프로필 연구소 등록 (우편엽서 본인확인은 연구소 주소로) → 본사 추가
5. **다음 주**: ③ 카카오맵 장소 등록 (연구소 → 본사)
6. **다음 주**: ④ Bing Webmaster 연결 + LinkedIn 회사 페이지
7. **20일 이내**: ⑤ 홈택스 사업자등록 주소 정정

위 순서면 외부 검색 결과에 빠르면 1주, 늦어도 4주 안에 **"[회사명]" 검색 시 [주소] 연구소가 첫 카드**로 노출됩니다.
