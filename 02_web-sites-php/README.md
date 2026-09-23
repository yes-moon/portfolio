# 기업 홈페이지 · 자체 PHP MVC 프레임워크

프레임워크 없이 코어부터 직접 만들어 구축하고 운영 중인 사이트 4종.

## 구성

- 뉴스 · 증서 갤러리 · 문의폼 · 관리자 페이지
- 라우터 · 컨트롤러 · 뷰 · DB 레이어를 직접 작성
- SSH 배포 스크립트 (Paramiko) — 변경 파일만 올리고 권한을 맞춘다

## 설계 메모

**DB 연결** — 요청당 커넥션 하나만 열고, 예외 모드와 프리페어드 스테이트먼트를 기본값으로 강제했다.
`PDO::ATTR_EMULATE_PREPARES => false`가 핵심이다. 기본값(에뮬레이션)에서는 드라이버가 문자열을 직접
끼워 넣기 때문에, 이 값을 꺼야 DB가 쿼리와 값을 분리해서 처리한다.

## 이 폴더

| 문서 | 내용 |
|---|---|
| `sites/company-main.example.com/GOAL.md` | 사이트 목표와 범위 |
| `sites/company-main.example.com/CHECKLIST.md` | 오픈 전 점검 항목 |
| `sites/company-main.example.com/INSIGHTS.md` | 운영하며 알게 된 것 |
| `sites/company-main.example.com/SEO_등록가이드.md` | 검색엔진 등록 절차 |
| `sites/stats-service.example.com/*` | 통계 서비스 사이트 문서 3종 |
| `sites/_home_renewal_20260703/` | 홈 리뉴얼 작업 로그 |

> 사이트 소스(`.php`)는 공개하지 않습니다. [대표 코드 발췌](../docs/코드-발췌.md#2-php-mvc--db-연결-싱글톤) 참고.
