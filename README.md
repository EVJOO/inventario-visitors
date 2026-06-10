# 인벤타리오 2026 문구페어 · 부스 가이드

방문객용 모바일 웹앱. 박람회 부스를 둘러보고 **찜 → 방문 체크 → 메모**를 남길 수 있어요.

🎪 **인벤타리오 2026 문구페어** · COEX THE PLATZ HALL 1 & 2 · 2026.06.10 — 14

## 주요 기능

- 📍 PLATZ HALL 1 / HALL 2 부스 배치도 (좌우 스크롤 + 확대)
- 📊 전체 115개 부스 중 방문 진행률
- 🔎 부스 검색 (이름 · 부스코드)
- ⭐️ 찜하기 / ✅ 방문 체크 / 📝 메모 — 행에서 바로 토글
- 🗂️ 알파벳순 · 카테고리별 정렬 탭
- 💾 모든 기록은 브라우저(localStorage)에 자동 저장

## 기술 스택

- 단일 `index.html` (vanilla JS · no build)
- 의존성: Pretendard + KkuBulLim 웹폰트 CDN
- 정적 호스팅: Vercel

## 로컬 실행

```bash
# Python 정적 서버
python3 -m http.server 8000

# 또는 그냥 브라우저에서 index.html 열기
open index.html
```

## 배포

Vercel에 자동 배포됨 — `index.html`이 repo 루트에 있어 별도 빌드 단계 없음.

## 카테고리 색

원본 배치도 PDF에서 추출한 8개 카테고리 색:

| 카테고리 | 색 |
|---|---|
| SMALL THING | `#F4B4C4` 라이트 핑크 |
| WORKSHOP & SHOWCASE | `#DCC400` 머스타드 옐로우 |
| WRITING & DRAWING | `#80DCF4` 라이트 시안 |
| DAILY FINDS | `#388824` 다크 그린 |
| PAPER | `#00B8F0` 브라이트 시안 |
| OFFICE & DESK | `#4A4A4A` 다크 그레이 |
| KIOSK | `#4CF448` 라임 그린 |
| COMMON | `#8995A8` 그레이 |
