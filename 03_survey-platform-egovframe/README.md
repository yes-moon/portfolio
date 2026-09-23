# 대학 교육수요자 만족도 통합조사 플랫폼

전자정부 표준 프레임워크(eGovFrame 4.x) 기반 조사 플랫폼.

<p align="center">
  <img src="src/main/webapp/img/illust/login.png" width="30%">
  <img src="src/main/webapp/img/illust/done.png" width="30%">
  <img src="src/main/webapp/img/illust/empty.png" width="30%">
</p>
<p align="center"><sub>행정 디지털 표준(KRDS) 톤에 맞춰 직접 그린 일러스트</sub></p>

## 설계의 핵심 — 순환하는 환류

조사 결과가 보고서로 끝나지 않도록 PDCA 순환을 시스템에 넣었다.

```
IPA 중점개선 항목 → 과제 등록 → 개선계획(Plan) → 실행(Do)
                                      ↓
              달성 / 이월(Act) ← 차년도 이행점검(Check)
```

연도가 서로 연결돼, 올해 이월된 과제가 내년 목록에 자동으로 올라온다.

## 제약에서 나온 판단

Oracle의 `VARCHAR2(2000 CHAR)`는 문자 수 기준이지만 물리적으로 4000바이트를 넘을 수 없다.
한글이 3바이트이므로 2000자를 채우면 6000바이트가 되어 들어가지 않는다.
그래서 입력 단에서 1000자로 막았다.

## 설계 문서

| 문서 | 내용 |
|---|---|
| [`설계_01_데이터모델설계서.md`](design-docs/설계_01_데이터모델설계서.md) | 테이블 정의와 관계 |
| [`설계_02_환류관리_상세설계.md`](design-docs/설계_02_환류관리_상세설계.md) | PDCA 순환 상세 |
| [`설계_03_기능-요구사항_대응표.md`](design-docs/설계_03_기능-요구사항_대응표.md) | 요구사항 ↔ 기능 매핑 |
| [`설계_04_화면목록.md`](design-docs/설계_04_화면목록.md) | 화면 구성 |
| [`설계_05_eGovFrame_골격설계.md`](design-docs/설계_05_eGovFrame_골격설계.md) | 패키지·레이어 구조 |
| [`시연가이드_사용법.md`](design-docs/시연가이드_사용법.md) | 발표 시연 흐름 |
| [`일러스트_제작가이드.md`](design-docs/일러스트_제작가이드.md) | 일러스트 톤·제작 기준 |
| [`개발환경_구축가이드.md`](design-docs/개발환경_구축가이드.md) · [`Tomcat_Eclipse_등록가이드.md`](design-docs/Tomcat_Eclipse_등록가이드.md) | 환경 구성 |

일러스트 시안 107점(SVG·PNG)이 `design-docs/일러스트`와 `src/main/webapp/img`에 있다.

> Java 소스와 Oracle DDL은 공개하지 않습니다. [대표 코드 발췌](../docs/코드-발췌.md#6-egovframe-환류관리--설계-의도를-코드에-남기기) 참고.
