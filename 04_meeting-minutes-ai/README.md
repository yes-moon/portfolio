# 회의록 AI

녹음 파일을 받아쓰고 화자를 나눈 뒤 회의록으로 정리하고, 그 내용에 질문까지 할 수 있다.

회의 내용이 외부 API로 나가면 안 된다는 요구사항이 있어 **전 과정을 로컬에서** 돌린다.

```
녹음 파일 ─→ Whisper STT ─→ 화자 분리 ─→ 로컬 LLM 회의록
                                              │
                                        bge 임베딩 ─→ RAG 질의응답
```

## 설계 메모

**조각 나누기** — 발화 구간 경계를 살리고 앞뒤를 겹치게 했다.
고정 길이로 자르면 문장 중간이 끊겨 검색 품질이 떨어진다.

**출처 표시** — 답변에 몇 분 몇 초 구간인지 함께 돌려줘, 사용자가 원본 녹음에서 바로 확인할 수 있다.
요약만 주면 믿을 근거가 없다.

## 두 가지 구현체

| | 구성 | 쓰임 |
|---|---|---|
| **(a)** Python 단일 앱 | FastAPI + SQLite, 설치 한 번으로 끝 | 개인·소규모 |
| **(b)** PHP 웹 + Python 워커 | 웹에서 업로드, 무거운 추론은 워커가 처리 | 여럿이 동시에 쓸 때 |

## 이 폴더

- [`meeton-note_python/docs/설계.md`](meeton-note_python/docs/설계.md) — 구조와 데이터 흐름
- [`meeton-note_python/docs/시연_대본.md`](meeton-note_python/docs/시연_대본.md) — 발표 시연 흐름
- [`meeton-note_python/docs/설치_노트북.md`](meeton-note_python/docs/설치_노트북.md) — 환경 구성
- [`meeton_php-web+worker/docs/README.md`](meeton_php-web+worker/docs/README.md) — 분리 아키텍처 설명

> 구현 소스는 공개하지 않습니다. [대표 코드 발췌](../docs/코드-발췌.md#4-회의록-ai--의미-검색) 참고.
