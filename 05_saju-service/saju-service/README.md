# saju-service — [사주브랜드] 사주박사

[사주브랜드] "[사주브랜드](金烏齋) 사주박사" 서비스. svc-core 기반이며 학습검사(sdl-service)와
고객·DB·토큰·배포가 완전히 분리된 독립 서비스다.

## 실행 (젯슨)

```bash
uvicorn saju_service.app:app --host 127.0.0.1 --port 8801   # API (nginx 뒤)
python -m saju_service.worker                                # 큐 워커 (systemd)
```

## 구성
- `products.py` — 상품·토큰가격·검수정책 (상담 1/3/7 확정, 나머지 임시값)
- `engine/` — 만세력 엔진 v2.0 (M3: 진태양시·표준시이력·서머타임·절입시각·야자시 보정 예정)
- `prompts.py` — 상담 프롬프트 (격리 원칙·인젝션 방어)
- `handlers.py` — LLM 초안 → 코드 검증(간지 대조) → 재생성 1회 → 검수행
- `routers.py` — /api/v1/saju/* (접수·상태·티켓)
- `worker.py` — 대기열 순차 처리

## .env (Git 제외 — 젯슨에서만 보관)
JWT_SECRET, SMTP_*, OLLAMA_MODEL(상업 라이선스만), GEMINI_API_KEY
