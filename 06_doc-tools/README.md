# 업무 문서 도구

반복 작업을 줄이려고 그때그때 만든 것들.

| 도구 | 하는 일 |
|---|---|
| HWP 텍스트 추출기 | `olefile`로 한글 문서에서 본문만 뽑아낸다 |
| Markdown → PDF 변환기 | `weasyprint` 기반. 보고서 서식 적용 |
| CSS 홀로그램 컬럼 데모 | 그라디언트·블렌드 모드 실험 |

## LLM 행동양식 이식 프롬프트

모델을 바꿔도 같은 방식으로 일하게 만들기 위해 작성한 프롬프트다.
말투를 흉내 내는 페르소나가 아니라, **결론을 형성하고 일을 수행하고 결과를 보고하는 과정**에 대한 규칙으로 썼다.

한국어판과 영어판이 있고, 각각 4만 자 안팎이다.

- [`prompt-engineering/fable5_행동양식_프롬프트.md`](prompt-engineering/fable5_행동양식_프롬프트.md)
- [`prompt-engineering/fable5_behavior_prompt_EN.md`](prompt-engineering/fable5_behavior_prompt_EN.md)
