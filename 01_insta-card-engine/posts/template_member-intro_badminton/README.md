# 배드민턴 동아리 인원소개 템플릿 「코트 + 셔틀콕」

> 엔진: `_engine\intro_template_badminton.py` (명찰 템플릿의 헬퍼를 가져다 씀). 1080x1350.

```
python _engine\intro_template_badminton.py     # 시안 3장 생성
```

## 회원 1장
```python
render_member_bd(
    role='회장', name='김민서',
    info=[('학부','체육교육과'), ('학번','23학번'), ('MBTI','ENFJ'), ('주종목','혼합복식')],  # 4행
    hashtags=['#스매시장인', '#코트지박령'],     # 손글씨, 2개 권장
    hand='코트 위에선 진지합니다',                 # 한마디 (14자 내외, `—` 금지)
    photo=r'D:\...\face.jpg', caption='2026. 03',  # 폴라로이드 사진·날짜
    page=2, total=7, out='02_회장.png')
```
- 폰트: 제목·이름 GmarketSans Bold / 손글씨 상해찬미체(강조)·잘하고있어(날짜) / 본문 Paperlogy
- 색 바꾸기: `C` 딕셔너리 (court=코트색, yellow=마커·테이프색)
- 다른 종목으로 바꾸려면 `shuttlecock()`만 다른 소품 함수로 교체하면 됨
