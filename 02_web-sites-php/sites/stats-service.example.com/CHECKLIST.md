# 작업 체크리스트

## 2026-05-08 완료 작업

### 상담게시판 일일 자동 게시 시스템 구축
- [x] 4/15 ~ 5/8 백필: 24일치 게시글 1일 1개씩 일괄 생성 (#5510 ~ #5533)
- [x] `auto_board_post.php` 일일 모드로 재작성 (기존 주1회 → 매일 1회)
- [x] 동일 날짜 중복 방지 체크 (`hasAutoPostForDate`)
- [x] `is_auto: true` 플래그로 자동 게시글 식별
- [x] `backfill_board_posts.php` 일회성 백필 스크립트 작성
- [x] Cron 변경: `0 9 * * 1` (월요일) → `0 9 * * *` (매일 09:00)
- [x] 백업: `/root/board_backup_20260508_005707/`, `auto_board_post.php.bak.20260508`

### 이메일 알림 정책
- [x] 자동 게시글: JSON 직접 작성으로 `savePost()` 우회 → **이메일 미발송**
- [x] 외부 실제 문의: `board_write.php` → `savePost()` → 관리자/사용자 알림 정상 동작 (변경 없음)

### 검증
- [x] board.php HTTP 200, 모든 카테고리(교육/경영/심리/간호/사복/행정/체육/유아/특수/의학/약학) 노출 확인
- [x] 4/15 ~ 5/8 24일/24일 모두 자동 게시글 1개 존재 확인
- [x] PHP 구문 검사 통과
- [x] Apache 에러 로그 클린

---

## 2026-02-04 완료 작업

### 게시판 수정/삭제 기능 구현
- [x] board_view.php에 수정/삭제 버튼 추가
- [x] board_edit.php 생성 (게시글 수정 페이지)
- [x] board_functions.php에 deletePost(), updateUserPost() 함수 추가
- [x] 삭제 시 첨부파일 동시 삭제 기능 (realpath 사용)
- [x] 수정/삭제 완료 메시지 표시

### 검색 기능
- [x] 게시판 검색 기능 추가 (제목/작성자/글번호/전체)
- [x] getAllPosts() 함수에 검색 파라미터 추가
- [x] 검색 결과 페이지네이션 유지

### UI 수정
- [x] 모든 페이지 상담 링크를 board.php로 변경
  - index.php, services.php, about.php, process.php
- [x] 헤더에 상담게시판 메뉴 추가 (config.php)
- [x] 메인페이지 첫 섹션 "무료 상담 신청" 버튼 삭제

### 관리자 페이지
- [x] admin/index.php 생성 (리다이렉트 처리)
  - 로그인 안됨 → login.php
  - 로그인 됨 → dashboard.php

### 버그 수정
- [x] board_edit.php session_start() 누락 수정
- [x] 첨부파일 삭제 경로 문제 수정 (realpath 사용)

---

## 수정된 파일 목록

### 2026-05-08 추가
| 파일 | 변경 내용 |
|------|----------|
| auto_board_post.php | **재작성** — 일일 모드, `createAutoPost()` 함수 분리, 중복 방지 체크 |
| backfill_board_posts.php | **신규** — 4/15 ~ 어제까지 일괄 생성용 (재실행 안전) |
| crontab (root) | `0 9 * * 1` → `0 9 * * *` (주1회 → 매일) |

### 2026-02-04 신규 생성
| 파일 | 설명 |
|------|------|
| board_edit.php | 게시글 수정 페이지 |
| admin/index.php | 관리자 인덱스 (리다이렉트) |
| GOAL.md | 프로젝트 목표 문서 |
| CHECKLIST.md | 작업 체크리스트 |
| INSIGHTS.md | 기술 인사이트 |

### 2026-02-04 수정됨
| 파일 | 변경 내용 |
|------|----------|
| board.php | 검색 기능, 삭제 메시지 |
| board_view.php | 수정/삭제 버튼, 수정완료 메시지 |
| board_functions.php | deletePost 개선, updateUserPost 추가, 검색 기능 |
| index.php | 무료상담 버튼 삭제, 링크 변경 |
| services.php | 상담 링크 변경 |
| about.php | 상담 링크 변경 |
| process.php | 상담 링크 변경 |

---

## 관리자 정보
- **URL**: https://stats-service.example.com/admin/
- **아이디**: admin
- **비밀번호**: ***REDACTED***

---

## 향후 작업 (미정)
- [ ] SSL 인증서 확인
- [ ] SEO 최적화
- [ ] 이미지 최적화
- [ ] 관리자 페이지 기능 개선

---

## 자동 게시 시스템 운영 가이드 (2026-05-08~)

### 동작
- 매일 **09:00 KST**에 cron이 `auto_board_post.php` 실행
- 같은 날짜에 자동 게시글이 이미 있으면 건너뜀 (`is_auto: true` 체크)
- 7개 학과 그룹 × 5개 템플릿 = 35종 중 날짜 시드 기반 결정적 선택

### 수동 실행
```bash
# 오늘 1개 생성 (이미 있으면 skip)
sudo -u www-data php /var/www/stats-service.example.com/auto_board_post.php

# 특정 날짜 범위 백필 (재실행 안전, 중복은 자동 skip)
sudo -u www-data php /var/www/stats-service.example.com/backfill_board_posts.php 2026-04-15 2026-05-07
```

### 실행 로그 / 상태 확인
```bash
# cron 실행 로그
tail -f /var/log/auto_board.log

# 자동 게시글 통계
php -r 'foreach(glob("/var/www/stats-service.example.com/data/board/post_*.json") as $f){$p=json_decode(file_get_contents($f),true); if(!empty($p["is_auto"])) echo $p["created_at"]." #".$p["number"]." ".$p["title"]."\n";}' | sort
```

### 자동 게시글 일괄 삭제 (필요 시)
```bash
php -r 'foreach(glob("/var/www/stats-service.example.com/data/board/post_*.json") as $f){$p=json_decode(file_get_contents($f),true); if(!empty($p["is_auto"])) unlink($f);}'
```

### 백업 위치
- 게시판 원본: `/root/board_backup_20260508_005707/board/` (109개 파일)
- 기존 스크립트: `/var/www/stats-service.example.com/auto_board_post.php.bak.20260508`
