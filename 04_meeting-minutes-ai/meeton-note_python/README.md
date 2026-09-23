# 미팅온 노트 (MeetOn Note)

온프레미스 회의 녹음·전사·회의록·검색 데스크톱 앱. 노트북 한 대에서 인터넷 없이 동작.
미팅온(MeetOn, 관계기관 회의 기록·이행관리 시스템)의 회의록 생성·RAG 엔진을 단독 구성한 판.

- **처음 읽을 것: `00_배경_이_프로그램과_사업의_맥락.md`** — 회사·입찰·미팅온·이 프로그램·발표 원칙을 한 문서로 정리
- 실행: `START.bat` (서버 + Edge 앱 창) 또는 `python -m app.server` → http://127.0.0.1:8765/
- 설치: `docs/설치_노트북.md` · 설계: `docs/설계.md` · 발표 시연: `docs/시연_대본.md`
- 구성: FastAPI(app/) + 브라우저 UI(web/) · faster-whisper(small 라이브 / medium 최종) · Ollama(qwen2.5:3b 질의 / qwen2.5:7b 회의록·화자 / bge-m3 임베딩) · SQLite + numpy 벡터스토어(data/)
- 시험: `_test/e2e.py` — 가짜 마이크에 TTS 회의 음성을 넣어 녹음→전사→회의록→질의→사진→내보내기 전 과정 자동 검증

폴더
```
app/       server.py(API) config.py db.py stt.py llm.py rag.py export.py
web/       index.html app.css app.js
prompts/   minutes_ko.txt(미팅온 원본+보충) speaker_ko.txt qa_ko.txt
data/      meeton_note.db · meetings/<id>/(rec_*.webm, photos/, export/) · rag/(corpus.jsonl, embeddings.npy, meta.json)
models/    faster-whisper 캐시 (medium·small)
docs/      설계 · 설치 · 시연 대본
_test/     e2e.py · TTS 시험 음성 · 시연용 회의 원고(seed*)
```
