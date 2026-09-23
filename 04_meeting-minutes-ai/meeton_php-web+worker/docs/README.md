# 미팅온 (MeetOn) — 관계기관 회의 기록·이행관리 시스템 v0.1

[A대학교] 「[A시]교육발전특구사업 3년 종합성과보고서 및 교육혁신선도지역사업 계획서 작성 용역」 과업지시서의
**관계기관 회의·미팅 15회 이상(기관별 5회) · 회의록 · 관리대장 · 최종보고서 반영여부 장** 요구를 자동화한다.
저작권 등록(컴퓨터프로그램저작물) 및 제안 발표 시연용 최소 구성.

## 구조
```
[호스팅사] 웹서버 (PHP 8.1 + MariaDB)                로컬 워크스테이션 (RTX 3090×2, Ollama)
┌──────────────────────────────┐   pull  ┌──────────────────────────────┐
│ 회의 등록(메모/녹음) → 큐      │ ──────▶ │ meeton_worker.py              │
│ 회의록 검토·승인              │ ◀────── │  · faster-whisper STT(선택)   │
│ 관리대장 / 반영여부 보고 장    │  push   │  · qwen3.5:27b 회의록 JSON    │
│ /api/jobs/* (Bearer 토큰)     │         │  (외부 API 전송 없음)          │
└──────────────────────────────┘         └──────────────────────────────┘
```
- 서버에는 인바운드 포트를 열지 않는다. 워커가 20초 간격으로 큐를 pull 한다.
- 녹음 원본은 전사 완료 후 서버에서 삭제. 텍스트만 보존.

## v0.2 추가 — 관계자 계정·2중 인증·RAG 질의 (2026-08-28)
- **역할별 대시보드**: viewer([A시]·교육지원청·특구센터·[A대학교]) = 우리 기관 회의·요청사항 반영현황·최신 회의록·RAG 바로가기 / admin·staff([회사명]) = 전체 이행률·작업 큐·계정 승인 대기·RAG 질의 현황·기한 임박 요청사항
- **계정 신청** `/apply`: 이름·기관·직위·업무메일·(선택)재직증빙(공문/재직증명서 PDF·JPG·HWP ≤10MB)·사유 → **업무 메일 인증코드(6자리, 10분)** → 접수(pending) → 관리자에게 알림 메일 → `/admin/users`에서 승인(역할·RAG 허용) → 임시 비밀번호 메일. 기관 도메인(korea.kr·cne.go.kr·citya.go.kr·univa.ac.kr) 일치 여부 자동 표시.
- **로그인 2중 인증**: 비밀번호 → 이메일 인증코드 → (선택) 30일 신뢰 기기. 이메일이 없는 계정(초기 admin)은 코드 없이 로그인되므로 배포 후 admin 이메일을 반드시 넣을 것.
- **성과자료 검색(RAG)** `/rag`: rag_access 계정이 질문 → `rag_queries` 큐 → 로컬 워커 `rag_answer.py`가 팩트온 저장소(bge-m3 top-8) + qwen 으로 출처 인용 답변 → 화면 5초 자동 갱신. 시간당 20건 제한.
- **메일**: `app/src/Mailer.php` 자체 SMTP(STARTTLS/SSL, 외부 라이브러리 없음). `.env`의 MAIL_* 설정. 재직증빙은 `uploads/evidence/`에 저장되며 웹 직접 접근 차단, 관리자만 열람.

## 화면 (데모 순서)
1. `/` 대시보드 — 총 15회 진행률, 기관별 5회 이행률, 요청사항 상태 집계
2. `/meetings/{id}` 회의 상세 — 입력 메모 ↔ AI 회의록(요약·논의·결정·회의결과) ↔ 기관별 요청사항·반영계획·반영결과
3. `/ledger` 관리대장 — 과업지시서 서식 그대로(실시일·기관명·참석자·안건·회의결과·조치담당자·반영결과), CSV
4. `/report` 반영여부 보고 — 최종보고서 별도 장(실시현황·회의별 결과·요청사항 반영여부·미반영 사유/조치계획) 인쇄

## 설치 (서버)
```bash
# 1) DB
mysql -u root -p < db/schema.sql
mysql -u root -p -e "CREATE USER 'meeton'@'localhost' IDENTIFIED BY '***REDACTED***'; GRANT ALL ON meeton.* TO 'meeton'@'localhost';"
# 2) 관리자 비밀번호 해시 갱신 (초기 placeholder 교체 필수)
php -r 'echo password_hash("원하는비밀번호", PASSWORD_BCRYPT), PHP_EOL;'
mysql meeton -e "UPDATE users SET pass_hash='***REDACTED***' WHERE login_id='company';"
# 3) 설정
cp app/config/.env.example app/config/.env   # DB_*, WORKER_TOKEN(openssl rand -hex 32), PROJECT_NAME
chmod 600 app/config/.env; chown -R www-data:www-data uploads
# 4) Apache: DocumentRoot 를 web/ 로, AllowOverride All (mod_rewrite)
# 5) 데모 시드 (선택)
mysql meeton < db/seed_demo.sql
```
로컬 미리보기: `php -S 127.0.0.1:8080 -t web` (라우터가 서브디렉터리·내장서버 모두 지원)

## 워커 (로컬)
```bash
cp worker/.env.example worker/.env     # BASE_URL, WORKER_TOKEN(서버와 동일), MODEL
python worker/meeton_worker.py --dry docs/sample_memo.txt   # 서버 없이 프롬프트 테스트
python worker/meeton_worker.py --once                        # 큐 비울 때까지 처리
python worker/meeton_worker.py                               # 상주
```
STT를 쓰려면 `pip install faster-whisper` (CUDA). 없으면 메모 입력만으로 동작.

## 권한
| role | 가능 |
|---|---|
| admin ([회사명]) | 전체 |
| staff (연구원) | 회의 등록·AI 생성·회의록 수정·승인·요청사항 갱신 |
| viewer ([A시]·교육지원청·특구센터) | 대시보드·회의·관리대장·보고 조회 |

## 팩트온(FactOn) RAG 자동 연계
승인된 회의록·요청사항은 `worker/rag_sync.py`가 기존 로컬 RAG 저장소(`D:\[A시]\_rag_poc` — corpus.jsonl / embeddings.npy / meta.json, bge-m3 1024d)에
**증분 반영**한다. 회의 1건 = 문서 1건(`rel=meeton/<id>_기관_회의명.txt`, folder=`2026_관계기관 회의록(미팅온)`), 내용 해시가 바뀐 회의만 청크 교체.
전체 재색인 없이 numpy 행 추가/삭제만 하므로 기존 5,853건 코퍼스는 건드리지 않는다. 워커가 `RAG_SYNC_SEC`(기본 10분) 간격으로 자동 실행.
```bash
python worker/rag_sync.py --dry     # 반영 대상 미리보기
python worker/meeton_worker.py --rag-sync
```
※ FactOn 대시보드(app.py)는 기동 시 벡터를 메모리에 올리므로 반영 후 재시작해야 검색된다. (app.py에 재로드 버튼을 넣는 것은 별도 작업)
※ 문제가 생기면 `.bak_meeton_YYYYMMDD` 백업 3종으로 복원.

## 워크스테이션 로컬 실행 (2026-08-28 구축)
포터블 MariaDB 11.4(`local\mariadb`, 127.0.0.1:3307) + PHP 8.3 내장 서버(127.0.0.1:8080) + 로컬 워커. `local\start_all.bat` 한 번에 기동. 계정·비밀번호는 `local\_admin_credentials.txt`. 상세 `local\README_로컬실행.md`.

## 폴더 (개발 홈: `D:\Workspace\meeton\`)
- `web/index.php` 라우트 · `web/app/src/` 프레임워크(Router/Db/View/Csrf/Auth) · `Controllers/` 5개 · `Views/`
- `web/db/schema.sql` 스키마(orgs·users·meetings·minutes·action_items·jobs) · `seed_demo.sql` 데모 회의 3건 메모
- `worker/meeton_worker.py` 회의록 워커 · `worker/rag_sync.py` RAG 연계 · `worker/prompts/minutes_ko.txt`
- `demo/시연_시나리오.md` 발표 시연 순서 · `copyright/` 저작권 접수 안내·소스 zip 생성기
- 서버 배포 대상: [호스팅사] `/var/www/meeton/` (배포 시 `web/` 내용만 올림, `.env`는 서버에서 직접 작성)
