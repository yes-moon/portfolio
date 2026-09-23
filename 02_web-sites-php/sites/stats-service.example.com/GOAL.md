# [논문통계브랜드] 웹사이트 (stats-service.example.com)

## 프로젝트 개요
통계 분석 컨설팅 서비스 웹사이트 - 모던 반응형 디자인

## 완료 상태: 95%

---

## 사이트 정보
| 항목 | 내용 |
|------|------|
| 도메인 | https://stats-service.example.com |
| 서버 | Synology NAS (nas.example.com) |
| 경로 | /volume1/web/stats-service.example.com/ |
| 기술스택 | PHP 8.0 + Tailwind CSS + Alpine.js |
| 데이터 | JSON 파일 기반 |

---

## 페이지 구성

### 공개 페이지
| 페이지 | 파일 | 상태 |
|--------|------|------|
| 메인 | index.php | ✅ |
| 회사소개 | about.php | ✅ |
| 서비스 안내 | services.php | ✅ |
| 진행 절차 | process.php | ✅ |
| FAQ | faq.php | ✅ |
| 문의하기 | contact.php | ✅ |
| 상담 게시판 | board.php | ✅ |
| 게시글 보기 | board_view.php | ✅ |
| 게시글 작성 | board_write.php | ✅ |
| 게시글 수정 | board_edit.php | ✅ |

### 관리자 페이지
| 페이지 | 파일 | 상태 |
|--------|------|------|
| 인덱스 | admin/index.php | ✅ |
| 로그인 | admin/login.php | ✅ |
| 대시보드 | admin/dashboard.php | ✅ |
| 게시판 관리 | admin/board.php | ✅ |
| 게시글 관리 | admin/board_view.php | ✅ |
| 문의 관리 | admin/inquiries.php | ✅ |

---

## 게시판 기능

### 사용자 기능
- [x] 비밀번호 기반 게시글 작성
- [x] 게시글 조회 (비밀번호 검증)
- [x] 게시글 검색 (제목/작성자/글번호/전체)
- [x] 게시글 수정 (본인 글)
- [x] 게시글 삭제 (본인 글 + 첨부파일)
- [x] 파일 첨부 (PDF, 이미지)
- [x] 추가 문의 작성
- [x] 페이지네이션

### 관리자 기능
- [x] 관리자 로그인/로그아웃
- [x] 게시글 목록 조회
- [x] 게시글 상세 보기
- [x] 관리자 답변 작성
- [x] 게시글 상태 변경
- [x] 이메일 알림 발송

---

## 이메일 알림
- [x] 새 게시글 → 관리자 알림
- [x] 관리자 답변 → 사용자 알림
- [x] 추가 문의 → 관리자 알림

---

## 최근 수정 (2026-02-04)
1. 게시글 수정/삭제 기능 추가
2. 첨부파일 동시 삭제 기능
3. 게시판 검색 기능
4. UI 링크 정리 (상담→board.php)
5. admin/index.php 리다이렉트 추가
6. 버그 수정 (세션, 파일 경로)

---

## 관리자 접속
- URL: https://stats-service.example.com/admin/
- ID: admin
- PW: ***REDACTED***
