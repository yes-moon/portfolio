# 설문 생성 자동화

SurveyMonkey API로 수십 문항짜리 설문을 코드로 만들어 배포한다.
관리 화면에서 문항을 하나씩 넣던 작업을, 문항 정의만 적으면 되도록 바꿨다.

```python
sb = SurveyBuilder(token="***REDACTED***")
sb.create_survey(title="테스트 설문", nickname="test_v1")
sb.add_single("성별을 선택해 주세요", ["남성", "여성", "기타"])
sb.add_matrix("만족도 평가", rows=["품질", "가격", "서비스"], choices=LIKERT5)
sb.print_summary()
```

## 설계 메모

**필수응답 규칙**은 API가 주는 값을 그대로 쓰지 않고 의미가 드러나는 상수로 감쌌다 —
`REQUIRED_ALL` · `REQUIRED_AT_LEAST_1` · `REQUIRED_EXACTLY_2` · `REQUIRED_AT_MOST_3`.

**IPA 문항**(만족도와 중요도를 한 표에서 동시에 묻는 형식)은 API가 2차원 매트릭스를
지원하지 않는다. 같은 항목으로 매트릭스를 두 번 만들어 이어 붙이는 방식으로 우회했다.

## 이 폴더

- [`서베이자동화.md`](서베이자동화.md) — 빌더 설계와 문항 유형별 사용법
- [`노트북_셋업_가이드.md`](노트북_셋업_가이드.md) — 실행 환경 구성

> 헬퍼 구현(`sm_helper.py`)과 실제 사용 예제 3종은 공개하지 않습니다.
