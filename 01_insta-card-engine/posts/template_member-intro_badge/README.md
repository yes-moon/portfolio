# 인원소개 템플릿 「명찰(ID Badge)」 — 사용법

> 엔진: `_engine\intro_template.py` (Python + Pillow). 브랜드 무관 범용 시안. 1080x1350.

## 렌더
```
python _engine\intro_template.py          # 시안 3장 생성 (이 폴더)
```
스크립트 하단 `__main__`에서 `render_cover(...)`, `render_member(...)` 호출을 인원수만큼 늘리면 됨.

## 회원 1장 파라미터
```python
render_member(
    role='회장', name='김민서',
    info=[('학부','경영학부'), ('학번','23학번'), ('MBTI','ENFJ')],   # 3줄 고정 권장
    tmi='계획은 완벽, 실행은 즉흥.',                                  # 포스트잇 한마디 (18자 내외)
    photo=r'D:\...\face.jpg',                                          # 없으면 실루엣 자리표시
    org='OO대학교 OO동아리', page=2, total=7, out='02_회장.png')
```
- 사진은 **정방형~세로 비율**이 잘 맞음(칸에 맞춰 중앙 크롭). 증명사진·상반신 권장.
- 회사용으로 쓰려면 `info`를 `('부서','R&D'), ('직책','수석연구원'), ('전문','머신러닝')` 식으로 바꾸고 `PAL` 팔레트만 교체.

## 팔레트 교체
`PAL` 딕셔너리 7색만 바꾸면 전체 톤이 바뀜. 현재 = 크림·세이지·차콜·코랄(범용). [회사명]용이면 딥네이비 배경 + 시안 강조로.

## 폰트
- 제목·이름: GmarketSans Bold (`글꼴\GmarketSansOTF`)
- 본문: Paperlogy 5·6 (`글꼴\paperlogy 글꼴`)
- 손글씨 느낌을 원하면 무료 한글 손글씨체(예: 나눔손글씨 계열) 1종 추가 후 `F_MED` 교체.

## 원본 참고
`source file\인스타 템플릿\` 18장 분석 결과는 CLAUDE.md 1-15절.
