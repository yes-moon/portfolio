# 프로젝트 인사이트

## 서버 정보

### Synology NAS 접속
```bash
# SSH 접속
sshpass -p "9Fpdlvldj!" ssh user@example.com

# 파일 업로드 (한글 경로 우회)
cat file.php | sshpass -p "9Fpdlvldj!" ssh user@example.com "cat > /volume1/web/stats-service.example.com/file.php"
```

### 경로 구조
```
/volume1/web/stats-service.example.com/     # 프로젝트 루트
├── index.php                     # 메인 페이지
├── about.php                     # 회사소개
├── services.php                  # 서비스 안내
├── process.php                   # 진행 절차
├── faq.php                       # FAQ
├── contact.php                   # 문의하기
├── board.php                     # 게시판 목록
├── board_view.php                # 게시글 보기
├── board_write.php               # 게시글 작성
├── board_edit.php                # 게시글 수정
├── includes/
│   ├── config.php                # 설정
│   ├── header.php                # 헤더
│   ├── footer.php                # 푸터
│   └── board_functions.php       # 게시판 함수
├── admin/
│   ├── index.php                 # 리다이렉트
│   ├── login.php                 # 로그인
│   ├── logout.php                # 로그아웃
│   ├── dashboard.php             # 대시보드
│   ├── board.php                 # 게시판 관리
│   ├── board_view.php            # 게시글 관리
│   └── inquiries.php             # 문의 관리
└── data/
    ├── board/                    # 게시글 JSON
    │   └── uploads/              # 첨부파일
    └── admin.json                # 관리자 정보
```

---

## 관리자 계정
| 항목 | 값 |
|------|-----|
| URL | https://stats-service.example.com/admin/ |
| 아이디 | admin |
| 비밀번호 | ***REDACTED*** |

---

## 기술적 발견

### 1. 한글 경로 문제
- macOS에서 한글 경로 포함 시 SCP 인코딩 오류 발생
- **해결**: /tmp로 복사 후 SSH cat 방식 사용

### 2. 세션 관리
- PHP $_SESSION 사용 시 반드시 `session_start()` 필요
- 페이지 상단에서 호출 확인

### 3. 파일 삭제
- 상대 경로 대신 `realpath()` 사용 권장
- `file_exists()` + `is_file()` 조합으로 안전 확인

### 4. 비밀번호 해시
- `password_hash()` / `password_verify()` 사용
- bcrypt 알고리즘 (PASSWORD_DEFAULT)

---

## 주요 함수 (board_functions.php)

### 게시글 관리
```php
createPost($data)                    // 생성
getPost($postId)                     // 단일 조회
findPost($idOrNumber)                // ID 또는 번호로 조회
getAllPosts($page, $perPage, $search, $searchType)  // 목록+검색
updateUserPost($postId, $data, $password)  // 사용자 수정
deletePost($postId)                  // 삭제 (첨부파일 포함)
```

### 인증
```php
verifyPostPassword($postId, $password)  // 게시글 비밀번호
verifyAdmin($username, $password)       // 관리자 로그인
isAdminLoggedIn()                       // 관리자 세션 확인
```

### 답변/댓글
```php
addAdminReply($postId, $content)        // 관리자 답변
addUserComment($postId, $content, $password)  // 사용자 추가 문의
```

### 알림
```php
notifyAdminNewPost($post)               // 새 게시글 알림
notifyUserReply($post, $reply)          // 답변 알림
notifyAdminNewComment($post, $comment)  // 추가 문의 알림
```

---

## SMTP 설정 (이메일 발송)
- 서버: smtp.naver.com:587
- 계정: config.php에서 확인
- TLS 사용

---

## 더미 데이터
- 100개 테스트 게시글 생성됨
- 기본 비밀번호: test1234
- 번호: 5401 ~ 5500
