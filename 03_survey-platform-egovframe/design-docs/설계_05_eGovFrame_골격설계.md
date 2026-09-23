# eGovFrame 프로젝트 골격 설계 (초안 v0.1)

> [A대학교] 교육 수요자 만족도 통합조사 플랫폼 / 작성 2026-08-07
> 근거: CLAUDE.md B-5 개발방침(전자정부 표준프레임워크 신규개발), SFR-001, DAR-001, SER-004
> 목적: 착수 시 바로 잡을 수 있는 아키텍처·패키지·공통모듈·연동 격리 설계. 코드는 착수 후 이 골격대로 생성.

---

## 1. 기술 스택 (제안서 '표준 프레임워크 적용'·'적용기술'에 기재)
| 구분 | 채택 | 비고 |
|---|---|---|
| 프레임워크 | **eGovFrame(전자정부 표준프레임워크) 5.0.2**(실행환경 5.0.0) | Spring 6 기반, 공공 표준 최신, 정성평가 가점(RFP "최신 버전"). 8/8 개발환경 설치 완료 |
| 언어/런타임 | **Java 21 (LTS)** | eGovFrame 5.0.2 필수 요건. 개발PC에 Temurin 21 설치완료. 학교 포털도 Java 계열(정합) |
| 웹계층 | Spring MVC(Spring 6) + JSP, **Jakarta EE 네임스페이스**(javax→jakarta) | eGov 5.0 표준. 신규개발이라 마이그레이션 부담 없음 |
| 영속계층 | MyBatis (eGov DataAccess) | Oracle SQL 직접 튜닝 용이(SE2 대응) |
| DB | **Oracle SE2**(운영) / Oracle Database Free(개발) | ★버전·기능 정합 주의(아래) |
| WAS | Tomcat(무료, 기본 제안) / 학교 표준 상용 시 대응 | 서버확인 후 확정 |
| 화면 | Bootstrap 반응형, 차트 라이브러리(오픈소스) | SFR-009 시각화 |
| 빌드 | Maven | eGov 표준 |
| 보안 | Spring Security + eGov 시큐어코딩, 행안부 개발보안가이드 | SER-003/004 |

> ※ 최종 라이브러리·버전은 "최신·검증된 버전"(RFP 요구)으로 착수 시 고정하고 산출물에 제품명·버전·라이선스 명시.

> **★검증으로 도출한 개발 DB 주의(함정)**: 개발용 Oracle Database Free는 **파티셔닝 등 EE 기능과 최신 버전 전용 문법(예: 23ai boolean/vector)을 허용**할 수 있다. 개발에서 무심코 쓰면 **운영 SE2에서 실패**한다(설계_01 0-1의 SE2 제약과 직결). 대응: ①운영 Oracle **메이저 버전을 방문 확인 후 개발 Free도 동일 계열로 맞춤**(19c면 19c 호환 문법) ②코딩 표준에 "파티셔닝·병렬힌트·EE 전용 기능 사용 금지" 명시 ③CI 단계에서 SE2 비호환 구문 정적점검. 이 항목은 서버 사양 확인(미확정)과 함께 착수 즉시 확정.

---

## 2. 패키지 구조 (도메인 계층형)

```
kr.ac.univa.survey
├─ common                      // 공통 (프레임워크 확장)
│   ├─ config                  // Spring/보안/데이터소스 설정
│   ├─ security                // 인증·인가, RBAC, 세션
│   ├─ code                    // 공통코드 캐시
│   ├─ file                    // 파일 업/다운(CM_FILE)
│   ├─ log                     // 접속/기능/다운로드 로그 AOP
│   ├─ exception               // 전역 예외·오류 메시지(PER-003)
│   └─ util                    // 공통 유틸(엑셀·PDF·암호화)
│
├─ integration                 // ★외부 연동 격리 (어댑터 패턴)
│   ├─ sso                     //   SsoAuthPort(인터페이스) + LocalStubSsoAdapter / UnivASsoAdapter
│   ├─ academic                //   AcademicSyncPort + FileStubAdapter / ViewAdapter / ApiAdapter
│   ├─ notify                  //   NotifyPort(email/sms/portal) + StubNotifier / RealNotifier
│   └─ readme.md               //   "규격 입수 전 Stub, 입수 후 Adapter 교체" 원칙
│
├─ system                      // 시스템관리 (SY 화면)
│   ├─ user  / role / dept / major / menu / commoncode / accesslog
│
├─ survey                      // 조사운영 (SV 화면)
│   ├─ survey                  //   조사 CRUD·상태·복사
│   ├─ question                //   문항은행·척도·이력
│   ├─ template                //   조사 템플릿
│   ├─ builder                 //   설문 편집(조사-문항 편성)
│   ├─ respondent              //   대상자(직접/엑셀/연계)
│   ├─ distribution            //   배포(URL/QR/이메일/문자/포털)
│   └─ response                //   응답 수집(임시저장·중복방지)
│
├─ analysis                    // 분석·보고 (AN 화면)
│   ├─ stat                    //   집계·기술통계·교차분석
│   ├─ dashboard               //   대시보드·시각화
│   └─ report                  //   보고서(엑셀/CSV/PDF)
│
├─ feedback                    // ★환류관리 (FB 화면) — 차별화
│   ├─ mapping                 //   문항-부서 매핑
│   ├─ task                    //   개선과제
│   ├─ plan                    //   개선계획
│   ├─ result                  //   실행결과·증빙
│   └─ link                    //   연도별 연계·비교
│
└─ portal                      // 통합포털·마이페이지·공지·자료실
```

각 도메인은 표준 계층: `web(Controller) → service(interface) → serviceImpl → mapper(MyBatis)` + `vo/dto`.

---

## 3. ★연동 격리 설계 (공백 3종을 개발 진행을 막지 않게)

### 3-1. 원칙
> 학교 규격(SSO·학사연동·문자/포털)이 아직 없다. 이걸 **Port 인터페이스**로 추상화하고, 지금은 **로컬 Stub 구현**으로 개발을 100% 진행한다. 규격이 오면 **Adapter 구현만 갈아끼운다**(도메인 코드 수정 0).

### 3-2. SSO 예시
```java
public interface SsoAuthPort {
    SsoUser authenticate(HttpServletRequest req); // 인증된 사용자 반환
}

// 개발단계: 로컬 로그인으로 대체
@Profile("local")
class LocalStubSsoAdapter implements SsoAuthPort { ... }  // ID/PW 폼 로그인

// 계약 후: 학교 SSO 규격에 맞춰 구현 (에이전트/토큰/SAML 중 무엇이든 여기서만)
@Profile("prod")
class UnivASsoAdapter implements SsoAuthPort { ... }
```

### 3-3. 학사연동·알림도 동일 패턴
- AcademicSyncPort: 사용자/부서/학과 적재. Stub=엑셀/CSV 적재, 실구현=뷰/API/배치 중 확정된 방식.
- NotifyPort: send(email|sms|portal). Stub=로그 출력, 실구현=학교 채널.

### 3-4. 효과 (제안서 '위험관리'에 기재)
- 연동 규격 지연이 **전체 일정을 막지 않음** → 리스크 격리.
- 단위테스트가 Stub으로 항상 가능 → 품질(QUR-001) 유리.

---

## 4. 공통 관심사 (횡단)
- **인증·인가**: Spring Security + RBAC(CM_ROLE_MENU). 메뉴/기능/행(부서) 3단 권한. 환류 부서담당자 행수준 보안(설계_02 4-2).
- **로깅**: AOP로 접속/기능/다운로드 자동 적재(CM_LOG_*), COR-001 이력관리.
- **시큐어코딩**: 파라미터 바인딩(MyBatis #{}), 출력 인코딩(XSS), CSRF 토큰, 파일 업로드 화이트리스트, 관리자페이지 비노출 — 행안부 가이드 체크리스트 매핑(SER-004).
- **개인정보 암호화**: [암] 컬럼 양방향 암호화(운영), 로그 마스킹.
- **오류 처리**: 전역 예외 핸들러 → 사용자 인지 가능 메시지 3초 이내(PER-003).
- **성능**: 집계 스냅샷(SV_STAT_SUMMARY) + 인덱스 + 쿼리표준(연도조건 선두) — SE2 대응(설계_01 0-1).

---

## 5. 환경 분리 (원격지 개발 방침 대응 — CLAUDE.md B-5)
| 프로파일 | 용도 | DB | 연동 |
|---|---|---|---|
| local | 사무실 개발 | Oracle Free 23ai | 전부 Stub |
| dev | 통합개발 | Oracle Free/학내 유사 | 일부 Adapter |
| prod | 학내 서버 | Oracle SE2 | 실 Adapter |
- 개발 데이터·시험 소스는 완료 후 소거(COR-001, 보안지침). 반입장비 보안설정(CMOS·화면보호 10분) 준수.

---

## 6. 착수 후 개발 순서 (골격 → 기능, 6개월 일정 정합)
1. (M1) 골격 생성 + 공통모듈(인증·코드·로그·파일·예외) + Port/Stub → **로그인·권한 동작하는 뼈대**
2. (M1~2) 기준정보(사용자·부서·학과·코드) + 조사/문항은행/설문편집
3. (M2~4) 대상자·배포·응답수집 / 통계·대시보드 / **환류관리**
4. (M4~5) 단위·통합·부하테스트 + 시큐어코딩·접근성 점검
5. (M5~6) Pilot·교육·매뉴얼·완료보고, 학내서버 이관·실연동 Adapter 적용

## 7. 미확정 (학교 확인 후)
- [ ] WAS 확정(Tomcat vs 학교 상용) → 배포·라이브러리 정합
- [ ] SSO/학사/알림 실제 방식 → Adapter 구현
- [ ] 학교 Java/빌드 표준·산출물 양식 → 정합
