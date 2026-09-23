# [회사명] 웹사이트 인사이트

## WordPress 데이터 분석 결과
- 기존 사이트: WordPress 6.2.2 + FinancePro 테마
- 총 43개 블로그 게시글 발견 (2017~2024)
- 99개 페이지, 203개 이미지 첨부파일
- 핵심 콘텐츠: AI/ML 서비스, 학술 협력, 특허, 수상 관련 뉴스

## 설계 결정사항
- 프레임워크 없이 순수 PHP MVC로 경량화
- Tailwind CSS CDN으로 빌드 도구 불필요
- TinyMCE CDN으로 관리자 에디터 제공
- CSRF 토큰으로 폼 보안 강화
- PDO prepare/execute로 SQL 인젝션 방지
- bcrypt로 비밀번호 해싱
- finfo_file로 업로드 MIME 타입 검증

## 참고사항
- 카카오맵 iframe 키: 2ge4r (설계 문서 참조)
- 이미지 자산은 WordPress 서버에서 별도 다운로드 필요
- TinyMCE API 키는 무료 버전 사용 (no-api-key)
