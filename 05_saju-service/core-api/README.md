# svc-core

[회사명] 서비스 공용 프레임워크. **[사주브랜드] 사주박사**(saju-service)와 **자기주도학습
성향검사**(sdl-service)가 부품으로 가져다 쓰는 라이브러리이며, 두 서비스는 고객·DB·토큰·배포가
완전히 분리된 별개 사업이다 (통합 멤버십 없음).

## 모듈

| 모듈 | 역할 |
|---|---|
| `config` | 서비스가 주입하는 설정 (DB 경로, SMTP, LLM, 서비스 슬러그) |
| `db` / `models` | SQLite 스토어 + 공통 스키마 (고객·이메일·대상자·신청·결과·문답·토큰장부·선물·쿠폰·티켓) |
| `identity` | 이메일 6자리 코드 인증, 불변 고객ID, 이메일 병합 |
| `wallet` | 토큰 장부(불변 tx) — 충전·차감·선물·쿠폰·잔액 |
| `queue` | 신청 상태머신: queued → processing → review(검수) → done / failed |
| `llm` | LLM 어댑터: Ollama(로컬) / Gemini(외부, 가명 데이터만) |
| `validator` | 응답 검증기 — 형식·분량·금칙 + 서비스별 훅(예: 만세력 엔진값 대조) |
| `mailer` | SMTP 발송 (인증코드·결과지·알림) |
| `api` | FastAPI 공통 라우터 팩토리 (/api/v1: 인증·헬스·지갑) |

## 설계 원칙 (종합설계 v2)

1. 이력 연동은 벡터 RAG가 아니라 **DB 정확 조회** — 문답은 결과ID에 앵커, 서비스 간 격리
2. 실명 대신 **가명(호칭)** 기본 — 외부 LLM에는 가명 데이터만 전송
3. 신원은 **불변 고객ID** — 이메일은 다중 등록 가능한 포인터 (비회원→회원 전환 시 이력 보존)
4. 토큰은 잔액이 아니라 **불변 장부** — 모든 증감이 tx로 기록, 잔액은 합계
5. 검수 파이프라인 — LLM 초안 → 코드 검증기 → (상품별 설정) 관리자 검수 → 발송, 수정율 기록

## 사용 (서비스 쪽)

```python
from svc_core.config import CoreSettings
from svc_core.api import create_app

settings = CoreSettings(service_slug="saju", db_path="saju.db", ...)
app = create_app(settings)   # 서비스 라우터를 여기에 추가
```
