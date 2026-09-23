# [고객기관] 설문 수집기 — 배포 가이드

> 위치(로컬): `d:\서베이 자동화\survey\`  (서베이 자동화 프로젝트 폴더로 통합)
> 배포 대상(서버): `/var/www/company-main.example.com/survey/`
> **기존 홈페이지·MVC를 한 줄도 건드리지 않는 격리 모듈.** (별도 폴더 + 자체 `.htaccess` + 전용 DB)

---

## 0. 무엇인가
[고객기관] 교육프로그램 만족도조사 **2종**(1차 온라인강의 / 2차 현장탐방)을
[회사명] 서버에서 **수집만** 하고, 원본을 **CSV + SPSS 신택스(.sps)** 로 내려받아
**분석은 워크스테이션**에서 하는 구조.

- 서버 역할: 응답 폼 호스팅 + 응답 저장 + 원본 export (설문 안내 메일·SMS 발송 없음)
- 개인정보(2차 휴대폰): 별도 테이블 평문 저장 + 관리자 전용 열람 + **일괄 파기** 기능
- 관리자 대시보드: 오늘/누적 응답수 · 7일 추이 · 설문별 다운로드
- **매일 수집현황 메일**(관리자 1통/일, 기본 ON, 대시보드에서 끄기 가능) — 대량발송 아님

## 1. 공개 URL
| 용도 | URL |
|------|-----|
| 1차 온라인강의 설문 | `https://company-main.example.com/survey/orga-online` |
| 2차 현장탐방 설문 | `https://company-main.example.com/survey/orga-visit` |
| 관리자 | `https://company-main.example.com/survey/admin` |

기본 관리자: **admin / orga2026!** → 배포 후 즉시 변경(§5)

## 2. 파일 구성
```
survey/
├── .htaccess         자체 라우팅(부모 .htaccess override) + 민감파일 차단
├── index.php         공개 라우터 + 레이아웃(CSS)
├── admin.php         관리자(로그인/대시보드/응답/export/파기/메일토글)
├── lib.php           렌더·검증·저장·집계·CSV/SPSS·설정·다이제스트
├── mail.php          SMTP 발송(STARTTLS, 외부의존 없음)
├── cron_daily.php    매일 수집현황 메일 발송(서버 cron)
├── surveys.php       설문 정의(문항·변수·값레이블)  ← 설문 추가/마감은 여기
├── config.php        전용 DB·솔트·세션·SMTP  ← 배포 전 비번/솔트/SMTP 변경
├── db.php            PDO 연결
├── schema.sql        전용 DB/계정/테이블 생성
├── views/ (form, thanks)
└── README_배포.md    (본 문서, 업로드 불필요)
```

## 3. 배포 전 변경 (보안 필수)
`config.php` 에서:
- `SV_DB_PASS` — 전용 DB 비밀번호 (schema.sql 의 계정 비번과 **동일**하게)
- `SV_IP_SALT` — 임의 문자열로 변경
- `SV_SMTP_PASS` — 매일메일 발송용 Naver SMTP 비밀번호 (없으면 메일만 실패, 수집은 정상)
- `SV_MAIL_TO` — 수집현황을 받을 관리자 메일 주소 확인

`schema.sql` 의 `IDENTIFIED BY '***REDACTED***' 도 같은 비번으로 맞출 것.

## 4. 서버 배포 절차
```bash
# (1) 전용 DB·계정·테이블 생성  — 기존 company2026 과 무관
scp schema.sql root@XXX.XXX.XXX.XXX:/tmp/
ssh root@XXX.XXX.XXX.XXX "mysql < /tmp/schema.sql && rm /tmp/schema.sql"
#  ↑ Windows에서는 paramiko 스크립트로 동일 수행(메모리 company-deploy-paramiko)

# (2) 앱 업로드 — survey/ 폴더만. 기존 파일 절대 덮어쓰지 않음
#     (README_배포.md, schema.sql 은 올려도 .htaccess가 직접접근 차단)
scp -r survey/ root@XXX.XXX.XXX.XXX:/var/www/company-main.example.com/

# (3) 권한
ssh root@XXX.XXX.XXX.XXX "chown -R www-data:www-data /var/www/company-main.example.com/survey"

# (4) 매일 수집현황 메일 cron 등록 (기본 18:00) — 관리자 1통/일
ssh root@XXX.XXX.XXX.XXX "(crontab -l 2>/dev/null; echo '0 18 * * * php /var/www/company-main.example.com/survey/cron_daily.php >> /var/log/survey_daily.log 2>&1') | crontab -"
```
> 로컬(Windows)에서는 위 ssh/scp 대신 paramiko 스크립트로 동일 수행(메모리 `company-deploy-paramiko`).
> 매일메일 on/off 는 **관리자 대시보드 버튼**으로 제어(cron은 항상 돌되, OFF면 발송 스킵). 끄려면 굳이 cron 삭제 불필요.

## 5. 배포 직후 확인 (체크리스트)
- [ ] `https://company-main.example.com/` 기존 홈페이지 **정상** (무손상 확인)
- [ ] `/survey/orga-online`, `/survey/orga-visit` 폼 표시
- [ ] 테스트 1건 제출 → "감사합니다" → `/survey/admin` 응답수 +1
- [ ] 관리자에서 CSV·SPSS 다운로드 정상
- [ ] 관리자 비밀번호 변경:
  ```bash
  php -r "echo password_hash('새비번', PASSWORD_BCRYPT, ['cost'=>12]);"
  # 결과를 사용:
  mysql company_survey -e "UPDATE sv_admin SET password=***REDACTED*** WHERE username='admin';"
  ```
- [ ] (2차) 연락처 CSV 다운로드/파기 동작 확인
- [ ] 대시보드 → "지금 테스트 발송" 클릭 → 관리자 메일 수신 확인(SMTP 설정 검증)
- [ ] 바탕화면 `[고객기관] 설문 관리자` 아이콘 클릭 → 관리자 로그인 화면 정상

## 6. 운영
- **설문 마감(수동)**: `surveys.php` 에서 `'status'=>'closed'` 로 변경 후 재업로드 → 즉시 종료 감사화면 표시.
- **설문 마감(자동)**: `'close_at'=>'2026-07-31 23:59'` 처럼 마감일시 지정 → 그 시각이 지나면 **자동으로 종료 감사화면**으로 전환(재업로드 불필요).
- **종료 감사 문구**: 기본값은 `surveys.php` 의 `SV_CLOSED_DEFAULT`. 설문별로 다르게 하려면 해당 설문에 `'closed_message'=>'...'` 지정.
- **데이터 회수**: 관리자 → 설문 상세 → ⬇ 원본 CSV + ⬇ SPSS 신택스 → 워크스테이션에서 `GET DATA` 로 불러와 분석.
- **개인정보 파기**: 기프티콘 발송 완료 후 관리자 → "연락처 일괄 파기" → phone 컬럼 NULL + 파기시각 기록.
- **설문 추가**: `surveys.php` 에 새 key 정의만 추가하면 `/survey/{key}` 로 자동 동작 (코드 수정 불필요).

## 7. 격리 보장 근거
- `/survey/` 는 실제 폴더 → 루트 `.htaccess` 의 `RewriteCond !-d` 에 의해 **기존 라우터(index.php) 미경유**.
- `/survey/.htaccess` 가 `RewriteEngine On` 으로 이 폴더 하위 라우팅을 자체 처리(부모 규칙 비상속).
- DB도 전용(`company_survey`) + 전용 계정 → 기존 `company2026` 무관.
- 따라서 **기존 홈페이지/뉴스/문의/관리자 어느 것도 영향 없음.**
