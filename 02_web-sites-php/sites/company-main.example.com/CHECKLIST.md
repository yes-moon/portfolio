# [회사명] 웹사이트 재제작 체크리스트

## Phase 1: 프로젝트 구조 및 코어
- [x] 디렉토리 구조 생성
- [x] .htaccess 라우팅 설정
- [x] composer.json 작성
- [x] config 파일 작성 (database, mail, app)
- [x] index.php 프론트 컨트롤러
- [x] core/Database.php (PDO 싱글톤)
- [x] core/Router.php (URL 라우팅)
- [x] core/Controller.php (베이스 컨트롤러)
- [x] core/Auth.php (세션 인증)
- [x] core/Request.php (요청 헬퍼)

## Phase 2: 모델
- [x] NewsModel.php
- [x] ContactModel.php
- [x] AdminUserModel.php

## Phase 3: 컨트롤러
- [x] HomeController.php
- [x] NewsController.php
- [x] ContactController.php (PHPMailer 연동)
- [x] admin/AdminAuthController.php
- [x] admin/AdminDashboardController.php
- [x] admin/AdminNewsController.php (CRUD + 이미지 업로드)
- [x] admin/AdminContactController.php

## Phase 4: 뷰 (프론트엔드)
- [x] layouts/main.php (공개 레이아웃)
- [x] layouts/admin.php (관리자 레이아웃)
- [x] home/index.php (히어로 + 서비스 + About + 문의폼 + 최신뉴스)
- [x] news/index.php (뉴스 목록 + 페이지네이션)
- [x] news/show.php (뉴스 상세)
- [x] contact/index.php (카카오맵 + 연락처 + 문의폼)

## Phase 5: 뷰 (관리자)
- [x] admin/login.php
- [x] admin/dashboard.php (통계 + 최근 뉴스/문의)
- [x] admin/news/index.php (글 목록 + 검색 + 삭제 모달)
- [x] admin/news/create.php (TinyMCE + 썸네일 업로드)
- [x] admin/news/edit.php (수정 폼)
- [x] admin/contacts/index.php (문의 목록 + 상세 모달)

## Phase 6: 데이터베이스
- [x] schema.sql (테이블 + 인덱스 + 초기 데이터)
- [x] WordPress 기존 뉴스 마이그레이션 데이터 (16건)

## Phase 7: 배포
- [ ] Composer install
- [ ] MySQL 스키마 적용
- [ ] 관리자 비밀번호 설정
- [ ] config 파일 실서버 설정
- [ ] 서버 업로드 및 테스트

---

## 변경 이력

### 2026-05-15 — 회사 주소 변경 반영
구주소([주소]) → 신주소 본사/연구소 2곳.

- **본사 (HQ)**: [주소] X층
  - XF, [Building], [Address], [Address], Seoul, Republic of Korea
- **연구소 (R&D Center)**: [주소] XX층 XXXX호 (빅데이터·AI 연구소)
  - Unit XXX, XXF, [Building], [Address], [Address], Seoul, Republic of Korea

수정 파일:
- `company/app/views/contact/index.php` — 주소 카드 한·영 병기 본사/연구소 2블록, 카카오맵 좌표 새 본사로 변경
- `company/app/views/layouts/main.php` — 상단 헤더 바 본사 1줄, 푸터 "오시는 길 / Location" 본사+연구소 한·영 병기

배포: paramiko/SFTP 스크립트(`_upload_address_update.py`)로 서버 업로드, www-data 소유권 설정 완료. `https://company-main.example.com/` 및 `/contact` 실사이트 검증 통과.

### 2026-05-15 — 외부 검색 노출 SEO 패키지 (연구소 우선)
"[회사명]" 외부 검색 시 [주소] 빅데이터·AI 연구소가 먼저 잡히도록 사이트 내부 신호를 전면 보강.

사이트 내부 (적용·검증 완료):
- `company/app/views/layouts/main.php` head — Organization JSON-LD(top-level address = R&D, location[] R&D 먼저), 연구소 단독 LocalBusiness JSON-LD, OG·canonical·geo 메타태그, 키워드/디스크립션 R&D 우선 문구
- `company/sitemap.xml` 신규 — /, /news, /contact 3 URL
- `company/robots.txt` 신규 — Naver Yeti/NaverBot, Daumoa, Googlebot, Bingbot 명시 허용 + Sitemap 선언
- 검증: 운영 페이지 JSON-LD 2블록 모두 ConvertFrom-Json 파싱 성공, R&D 주소가 1순위로 노출됨

사이트 외부 (사용자 등록 필요):
- `SEO_등록가이드.md` 작성 — Google Search Console·비즈니스 프로필, 네이버 서치어드바이저·플레이스, 카카오맵, Bing, LinkedIn 등 등록 절차 + NAP(한·영) 입력 시트 + 등록 순서 추천
- 사장님 로그인·본인확인이 필요한 단계라 대리 등록 불가. 단, 등록 과정에서 발급되는 site-verification 메타태그는 카톡/메일로 전달해 주시면 즉시 head에 삽입 가능.

### 2026-05-15 — 2022 장관상 수상 라벨 정정
홈 첫화면 hero 통계 카드와 About 섹션 통계 카드의 짧은 라벨 "장관상 수상"을 정식 명칭으로 교체.

- `company/app/views/home/index.php` 2곳 — 큰 글씨 `2022`는 유지, 작은 라벨을 `[수상명] / ([부문]) 수상` 두 줄로 확장 (`leading-tight` + `<br>`)
- 배포: 일회성 paramiko 스크립트 `_upload_home_index.py` 사용 (기존 `_upload_address_update.py`는 주소 변경용 4파일 리스트로 고정돼 있어 분리)
- 검증: 운영 페이지 HTML에서 신 라벨 2회 검출, 옛 짧은 라벨 0회 잔존
