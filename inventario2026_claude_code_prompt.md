# Claude Code 빌드 프롬프트 — 인벤타리오 2026 문구페어 부스 가이드 (모바일 웹)

> 이 문서를 그대로 Claude Code에 붙여넣어 작업을 지시하세요. 아래 사양·토큰·데이터를 **그대로** 사용해 동일한 결과물을 만들면 됩니다.

---

## 0. 역할 & 목표

너는 프론트엔드 개발자다. 문구 박람회 **"인벤타리오 2026 문구페어"** 방문객용 **모바일 웹앱**을 만든다. 방문객이 부스를 둘러보고 **① 가고 싶은 부스를 별표(찜) → ② 방문 후 체크 → ③ 업체별 메모**를 남기는 것이 핵심이다. 시그니처는 방문 체크를 "도장 찍기"로 표현한 **스탬프 랠리**.

박람회 메타: **인벤타리오 2026 문구페어 · COEX THE PLATZ HALL 1&2 · 2026.06.10–14**

## 1. 결과물 & 제약

- **단일 파일 `index.html`** 하나로 완결(외부 JS/CSS 번들·빌드 단계 없음). 바닐라 JS만 사용(React/프레임워크 금지).
- **Vercel 정적 배포** 대상: repo 루트에 `index.html`만 두면 그대로 동작해야 한다.
- 외부 의존성은 **웹폰트 CDN**만 허용(아래 명시). 폰트 미로딩 시 시스템 폰트로 자연 폴백.
- 브라우저 저장은 **`localStorage`** 사용(로그인·서버 없음). 단, `localStorage` 접근 불가 환경에서는 인메모리로 폴백(아래 코드 그대로).
- 언어: **한국어 단일**. 다크모드 없음(라이트 전용). 모바일 세로 우선, 콘텐츠 최대 폭 480px 중앙 정렬.

## 2. 화면 구조 (하단 탭 3개 + 상세 시트)

하단 고정 내비게이션 탭: **지도 / 부스 / 내 리스트** (기본 진입 = 지도).

1. **지도** — 티켓 형태 히어로(박람회명·장소·날짜) + 부스 배치도 이미지(좌우 스크롤, 확대 토글) + 카테고리 색 범례(탭 시 해당 카테고리로 필터된 ‘부스’ 탭 이동).
2. **부스** — 검색 입력 + 필터 칩(전체 / ⭐찜만 / 미방문만 / 8개 카테고리) + 부스 카드 리스트. 카드: 카테고리 색 좌측 스트라이프, 부스코드 뱃지, 한글명(굵게)·영문명, 메모 있으면 “📝 메모” 태그, 우측에 ☆찜·⬜방문 토글 버튼. 카드 본문 탭 시 상세 시트.
3. **내 리스트** — 상단 **스탬프 랠리 카드**(진행률·도장) + “찜한 부스(미방문)” 목록 + “방문 완료”(메모 프리뷰) 목록 + “내 기록 전체 초기화” 버튼.

**상세 시트(바텀시트)**: 부스코드·카테고리칩·한글명·영문명, 큰 “찜하기/방문 체크” 토글 2개, 메모 textarea(입력 즉시 저장), 하단에 P1 안내(“🔜 준비 중: 인스타그램 · 리미티드 에디션 · 이벤트 위치”). 스크림 탭·그립 탭·ESC로 닫힘.

## 3. 기능 요구사항

**P0 (반드시 구현)**
- 115개 부스 데이터 렌더(부록 A). 중복 부스코드(E05·H06·L02) 존재 → 각 항목에 고유 `id` 부여(코드 아님).
- 찜(planned)·방문(visited) 토글: 카드/시트 어디서 눌러도 1탭 토글, 즉시 저장·반영.
- 메모: 부스별 자유 텍스트, 입력 즉시 저장, 카드/리스트에 표시.
- 검색: 한글명·영문명·부스코드 부분일치(대소문자 무시).
- 필터: 카테고리 단일선택 + ‘찜만’/‘미방문만’ 토글(중첩 가능). ‘전체’로 해제.
- 내 리스트: 진행률·도장, 찜/방문 목록, 전체 초기화(확인 후).
- 모든 상태 `localStorage` 지속(새로고침·재접속 유지). 상단에 저장 상태 점(저장됨/세션만) 표시.

**P1 (지금은 자리표시만, 데이터 추후)**
- 부스별 인스타그램 링크 · 이번 박람회 리미티드 에디션 배지/설명 · 박람회 이벤트 목록/위치.
- 상세 시트에 “준비 중” 안내 박스로 표기만 해두고, 데이터 모델에 `instagram?`, `limitedEdition?`, 이벤트 구조를 미리 비워둔다.

## 4. 디자인 시스템 (이대로 적용)

**무드:** 리소그래프·스티커 수집 문화 기반 **네오-브루탈리즘 팝** (geometric · cute · creative). 굵은 잉크 테두리 + 하드 오프셋 섀도우(`4px 4px 0`), 둥근 모서리, 비비드 컬러. 시그니처는 스탬프 랠리.

**폰트 (둘 다 웹폰트 CDN, 파일 첨부 불필요)**

`<head>`에 추가:
```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Black+Han+Sans&display=swap" rel="stylesheet">
<link rel="stylesheet" as="style" crossorigin href="https://cdn.jsdelivr.net/gh/orioncactus/pretendard@v1.3.9/dist/web/static/pretendard.css">
```
CSS에 추가(헤드라인용 배달의민족 꾸불림체, OFL):
```css
@font-face{font-family:'KkuBulLim';src:url('https://cdn.jsdelivr.net/gh/projectnoonnu/2410-1@1.0/BMkkubulimTTF-Regular.woff2') format('woff2');font-weight:normal;font-display:swap;}
```
- 본문/소형 라벨/부스코드 뱃지: **Pretendard** (가독성)
- 헤드라인/디스플레이(브랜드 워드마크·히어로 제목·스탬프 랠리 제목·시트 부스명·빈 상태 큰 글자): **`var(--display)`** = 꾸불림체
- 꾸불림체는 흐물거리는 손글씨류라 **소형 텍스트엔 쓰지 말 것**(코드 뱃지는 Pretendard 800).

**컬러 토큰 (`:root`에 그대로)**
```css
:root{
  --ink:#1C2230;
  --paper:#FBFDFF;
  --panel:#FFFFFF;
  --cyan:#0EB9EB;     /* 메인: 티켓 히어로·스탬프 랠리 카드·브랜드 액센트·포커스 링 */
  --green:#50F253;    /* 방문(체크)·진행률 바·완료 도장 */
  --yellow:#F6EB3C;   /* 찜(별표)·섹션 포인트 */
  --display:"KkuBulLim","Black Han Sans",system-ui,sans-serif;
  --line:2.5px solid var(--ink);
  --shadow:4px 4px 0 var(--ink);
  --shadow-sm:3px 3px 0 var(--ink);
  --radius:16px;
  /* 카테고리 8색 (길찾기용, 브랜드 3색과 별개로 유지) */
  --c-small:#FF5C8A; --c-workshop:#FFB23E; --c-writing:#3B7DFF; --c-common:#9B8AFF;
  --c-daily:#18BFA5; --c-paper:#57C84D; --c-office:#6D5BD0; --c-kiosk:#FF7A3D;
}
```
- 밝은 컬러 면(시안/그린/옐로우) 위 텍스트는 **잉크색(`--ink`)**으로 대비 확보. 흰 글자 금지.
- 본문 배경에 잉크색 도트 패턴(radial-gradient, 22px 간격)으로 은은한 질감.
- 포커스 링은 `outline:3px solid var(--cyan)`로 또렷하게.

**카테고리 → 한글 라벨 · 색 매핑 (JS `CATS` 객체 그대로)**
```js
const CATS = {
  "SMALL THING":        {ko:"스몰띵",        color:"var(--c-small)"},
  "WORKSHOP & SHOWCASE":{ko:"워크숍·쇼케이스",color:"var(--c-workshop)"},
  "WRITING & DRAWING":  {ko:"필기·드로잉",    color:"var(--c-writing)"},
  "COMMON":             {ko:"공용·라운지",    color:"var(--c-common)"},
  "DAILY FINDS":        {ko:"데일리 파인즈",  color:"var(--c-daily)"},
  "PAPER":              {ko:"페이퍼",        color:"var(--c-paper)"},
  "OFFICE & DESK":      {ko:"오피스·데스크",  color:"var(--c-office)"},
  "KIOSK":              {ko:"키오스크",      color:"var(--c-kiosk)"}
};
```

## 5. 데이터 모델

```
Booth { id, code, category, nameKo, nameEn,
        instagram?, limitedEdition?:{has,desc?,imageUrl?} }   // 뒤 3개는 P1
UserBoothState { planned:boolean, visited:boolean, memo:string }   // localStorage, id를 키로
```
부스 배열은 **부록 A** 그대로 사용한다(`id`는 이미 고유하게 부여됨). 카테고리 표시 순서는 부록 등장 순서(SMALL THING → WORKSHOP & SHOWCASE → WRITING & DRAWING → COMMON → DAILY FINDS → PAPER → OFFICE & DESK → KIOSK).

## 6. 저장 로직 (이 코드 그대로)

```js
const store = (()=>{
  const KEY="inventario2026_state"; let ok=false, mem={};
  try{localStorage.setItem("__t","1");localStorage.removeItem("__t");ok=true;}catch(e){}
  return {
    ok,
    load(){ if(ok){try{return JSON.parse(localStorage.getItem(KEY)||"{}");}catch(e){return{};}} return mem; },
    save(o){ if(ok){try{localStorage.setItem(KEY,JSON.stringify(o));return;}catch(e){}} mem=o; }
  };
})();
let state = store.load();            // { [id]: {planned,visited,memo} }
function st(id){ return state[id] || (state[id]={planned:false,visited:false,memo:""}); }
function persist(){ store.save(state); }
```
상단 저장 표시: `store.ok`이면 “저장됨”(초록 점), 아니면 “이 세션만 저장”.

## 7. 배치도(지도) 이미지 처리

- ‘지도’ 탭에 공식 부스 배치도를 `<img>`로 임베드한다. 폭 900px 기준 가로 스크롤(`overflow:auto`), “확대” 버튼으로 1500px 토글.
- 이미지는 **base64 data URI로 인라인**(단일 파일 유지) 또는 repo에 `map.png`로 두고 참조. 둘 다 가능.
- 원본 PDF(공식 플로어맵)에서 상단 다이어그램만 잘라 쓰면 된다. 재현 레시피:
```bash
pdftoppm -png -r 300 floormap.pdf page          # 페이지 래스터화
python3 - <<'PY'
from PIL import Image, base64, os
im=Image.open('page-1.png'); W,H=im.size
d=im.crop((0,0,W,int(H*0.42)))                   # 상단 약 42% = 홀 다이어그램
d=d.resize((1400,int(d.height*1400/d.width)))
d.save('map.png', optimize=True)                 # ~130KB
PY
# base64 인라인이 필요하면: import base64; "data:image/png;base64,"+base64.b64encode(open('map.png','rb').read()).decode()
```
- 지도 하단 안내문: “좌우로 밀어서 보기 · 부스 표시는 ‘부스’ 탭에서”. (지도는 오리엔테이션용, 개별 부스 마킹은 리스트에서 한다.)

## 8. 인터랙션 디테일

- **토글**: `st(id).planned/visited` 반전 → `persist()` → 영향받는 화면 다시 렌더. 별표 ☆/⭐, 방문 ⬜/✅.
- **상세 시트**: 열릴 때 해당 부스로 내용 채움. 메모 `oninput`마다 저장. 스크림/그립/ESC로 닫기. 닫을 때 리스트 새로고침.
- **스탬프 랠리 계산식**(내 리스트 상단):
  - `planned` = 찜한 부스들, `visited` = 방문한 부스들, `plannedVisited` = 찜 중 방문한 수, `denom = planned.length`.
  - 큰 숫자 = `visited.length`. 보조 라벨 = `denom`이면 “/ {denom} 찜 중 {plannedVisited} 방문”, 아니면 “방문한 부스”.
  - 진행률 = `denom ? round(plannedVisited/denom*100) : (visited.length?100:0)`.
  - 도장 = `(denom?planned:visited).slice(0,24)`; 방문 완료면 채워진 도장(그린·체크·살짝 회전), 아니면 점선 원에 부스코드.
- **빈 상태 문구**: 검색 결과 없음 → “조건에 맞는 부스가 없어요. 필터를 바꿔보세요.” / 찜 없음 → “‘부스’ 탭에서 ☆를 눌러 담아보세요.”
- **모션**: 화면 전환 페이드, 토글 살짝 스케일, 시트 슬라이드 업. `prefers-reduced-motion`이면 모두 제거.

## 9. 접근성 / 품질

- 모바일 세로 우선, 한 손 조작, 터치 타깃 ≥ 42px.
- 키보드 포커스 가시화(시안 아웃라인), 시맨틱 마크업(시트 `role="dialog"`, 토글 `aria-pressed`, 탭 `aria-current`).
- 첫 화면 2초 내 렌더, 핀/카드 100개+ 부드럽게.
- 사용자 입력(메모)은 표시 전 `<` 이스케이프.

## 10. 배포 (Vercel)

- repo 루트에 `index.html`(+선택적으로 `map.png`)만. Vercel “Import Project” → 프레임워크 없음(Other) → 즉시 정적 배포.
- 또는 폴더에서 `vercel` CLI 실행.
- (선택, P1 권장) 오프라인 대응 PWA: `manifest.json` + 서비스워커로 앱·폰트·맵 캐싱.

## 11. 수용 기준 체크리스트

- [ ] `index.html` 단일 파일로 빌드 없이 브라우저에서 동작
- [ ] 115개 부스, 8개 카테고리 색·한글 라벨 정확
- [ ] 찜/방문 토글이 카드·시트·내 리스트에서 일관되게 즉시 반영·저장
- [ ] 새로고침/재접속 후에도 찜·방문·메모 유지(`localStorage`), 불가 환경에선 세션 유지로 폴백
- [ ] 검색(한/영/코드)·필터(카테고리+찜만+미방문만) 정상
- [ ] 스탬프 랠리 진행률·도장 계산이 위 공식과 일치
- [ ] 폰트(본문 Pretendard / 헤드라인 꾸불림체)·브랜드 3색·네오브루탈리즘 스타일 적용
- [ ] 지도 탭에 배치도 이미지 + 카테고리 범례 + 스크롤/확대
- [ ] P1(인스타·리미티드·이벤트)은 “준비 중” 자리표시 + 데이터 모델 필드만 비워둠
- [ ] reduced-motion·포커스 가시화·터치 타깃 충족

---

## 부록 A. 부스 데이터 (115개, 그대로 사용)

```json
[
  {"id": "A02-1", "code": "A02", "category": "SMALL THING", "nameKo": "mt × 롤드페인트", "nameEn": "mt × rolledpaint"},
  {"id": "B02-2", "code": "B02", "category": "SMALL THING", "nameKo": "겨울엔토스트가좋아", "nameEn": "LIMPA LIMPA"},
  {"id": "B03-3", "code": "B03", "category": "SMALL THING", "nameKo": "후르츠후라이", "nameEn": "FRUITFRIES"},
  {"id": "B06-4", "code": "B06", "category": "SMALL THING", "nameKo": "이와코", "nameEn": "IWAKO"},
  {"id": "C03-5", "code": "C03", "category": "SMALL THING", "nameKo": "하우풀리소클럽", "nameEn": "HWF.RISOCLUB"},
  {"id": "E03-6", "code": "E03", "category": "SMALL THING", "nameKo": "구버", "nameEn": "GOOBER"},
  {"id": "E05-7", "code": "E05", "category": "SMALL THING", "nameKo": "미씨아", "nameEn": "MICIA"},
  {"id": "E05-8", "code": "E05", "category": "SMALL THING", "nameKo": "리라", "nameEn": "LYRA"},
  {"id": "F01-9", "code": "F01", "category": "SMALL THING", "nameKo": "라이프앤피시스", "nameEn": "LIFE&PIECES"},
  {"id": "F03-10", "code": "F03", "category": "SMALL THING", "nameKo": "이로하", "nameEn": "IROHA"},
  {"id": "F07-11", "code": "F07", "category": "SMALL THING", "nameKo": "플래그", "nameEn": "FLAGG"},
  {"id": "F08-12", "code": "F08", "category": "SMALL THING", "nameKo": "썸무드디자인", "nameEn": "SOME MOOD DESIGN"},
  {"id": "F09-13", "code": "F09", "category": "SMALL THING", "nameKo": "보키", "nameEn": "BOKI"},
  {"id": "G03-14", "code": "G03", "category": "SMALL THING", "nameKo": "수키도키", "nameEn": "SUKEYDOKEY"},
  {"id": "G06-15", "code": "G06", "category": "SMALL THING", "nameKo": "아무개씨", "nameEn": "AHMUGAE_C"},
  {"id": "G07-16", "code": "G07", "category": "SMALL THING", "nameKo": "산비", "nameEn": "SANBY"},
  {"id": "H02-17", "code": "H02", "category": "SMALL THING", "nameKo": "0.1", "nameEn": "0.1"},
  {"id": "H05-18", "code": "H05", "category": "SMALL THING", "nameKo": "미니모니", "nameEn": "MINIMONI"},
  {"id": "H06-19", "code": "H06", "category": "SMALL THING", "nameKo": "히토토키", "nameEn": "HITOTOKI"},
  {"id": "H07-20", "code": "H07", "category": "SMALL THING", "nameKo": "프레젠트프레젠트", "nameEn": "PRESENT PRESENT"},
  {"id": "H09-21", "code": "H09", "category": "SMALL THING", "nameKo": "키오스크키오스크", "nameEn": "KIOSKKIOSK"},
  {"id": "I08-22", "code": "I08", "category": "SMALL THING", "nameKo": "리틀템포디자인", "nameEn": "LITTLE TEMPO DESIGN"},
  {"id": "I09-23", "code": "I09", "category": "SMALL THING", "nameKo": "아이코닉", "nameEn": "ICONIC"},
  {"id": "J03-24", "code": "J03", "category": "SMALL THING", "nameKo": "프렐류드스튜디오", "nameEn": "PRELUDE STUDIO"},
  {"id": "J09-25", "code": "J09", "category": "SMALL THING", "nameKo": "노낫네버", "nameEn": "NO NOT NEVER"},
  {"id": "J10-26", "code": "J10", "category": "SMALL THING", "nameKo": "풀풀", "nameEn": "PULPUL"},
  {"id": "L02-27", "code": "L02", "category": "SMALL THING", "nameKo": "라이플페이퍼", "nameEn": "RIFLE PAPER CO."},
  {"id": "L04-28", "code": "L04", "category": "SMALL THING", "nameKo": "지헤이", "nameEn": "GEEHEY"},
  {"id": "L06-29", "code": "L06", "category": "SMALL THING", "nameKo": "슈아뜰리에", "nameEn": "SUATELIER"},
  {"id": "A07-30", "code": "A07", "category": "WORKSHOP & SHOWCASE", "nameKo": "더한글", "nameEn": "THE HANGEUL"},
  {"id": "B07-31", "code": "B07", "category": "WORKSHOP & SHOWCASE", "nameKo": "서울디자인재단 × 서울스티커샵", "nameEn": "SEOUL DESIGN FOUNDATION"},
  {"id": "C07-32", "code": "C07", "category": "WORKSHOP & SHOWCASE", "nameKo": "파티", "nameEn": "PaTI"},
  {"id": "C09-33", "code": "C09", "category": "WORKSHOP & SHOWCASE", "nameKo": "삼원페이퍼 워크숍", "nameEn": "SAMWON PAPER WORKSHOP"},
  {"id": "D04-34", "code": "D04", "category": "WORKSHOP & SHOWCASE", "nameKo": "키오스크키오스크 문구클럽 & 워크숍", "nameEn": "KIOSKKIOSK MUNGU CLUB & WORKSHOP"},
  {"id": "L09-35", "code": "L09", "category": "WORKSHOP & SHOWCASE", "nameKo": "트래블러스컴퍼니 워크숍", "nameEn": "TRAVELER'S COMPANY WORKSHOP"},
  {"id": "A03-36", "code": "A03", "category": "WRITING & DRAWING", "nameKo": "파버카스텔", "nameEn": "FABER-CASTELL"},
  {"id": "A04-37", "code": "A04", "category": "WRITING & DRAWING", "nameKo": "카키모리", "nameEn": "KAKIMORI"},
  {"id": "A05-38", "code": "A05", "category": "WRITING & DRAWING", "nameKo": "흑심", "nameEn": "BLACKHEART"},
  {"id": "A08-39", "code": "A08", "category": "WRITING & DRAWING", "nameKo": "까렌다쉬", "nameEn": "CARAN D'ACHE"},
  {"id": "A09-40", "code": "A09", "category": "WRITING & DRAWING", "nameKo": "세일러", "nameEn": "SAILOR"},
  {"id": "A10-41", "code": "A10", "category": "WRITING & DRAWING", "nameKo": "쉴드물감", "nameEn": "SHIELDCOLORS"},
  {"id": "A11-42", "code": "A11", "category": "WRITING & DRAWING", "nameKo": "코픽", "nameEn": "COPIC"},
  {"id": "C02-43", "code": "C02", "category": "WRITING & DRAWING", "nameKo": "글입다", "nameEn": "WEARINGEUL"},
  {"id": "E01-44", "code": "E01", "category": "WRITING & DRAWING", "nameKo": "도미넌트인더스트리", "nameEn": "DOMINANT INDUSTRY"},
  {"id": "E04-45", "code": "E04", "category": "WRITING & DRAWING", "nameKo": "한국파이롯트", "nameEn": "KOREA PILOT"},
  {"id": "F06-46", "code": "F06", "category": "WRITING & DRAWING", "nameKo": "제브라", "nameEn": "ZEBRA"},
  {"id": "H06-47", "code": "H06", "category": "WRITING & DRAWING", "nameKo": "펜텔", "nameEn": "PENTEL"},
  {"id": "H06-48", "code": "H06", "category": "WRITING & DRAWING", "nameKo": "레이메이 켑트", "nameEn": "RAYMAY KEPT"},
  {"id": "H06-49", "code": "H06", "category": "WRITING & DRAWING", "nameKo": "우디", "nameEn": "WOODHI"},
  {"id": "J01-50", "code": "J01", "category": "WRITING & DRAWING", "nameKo": "유니", "nameEn": "UNI"},
  {"id": "J07-51", "code": "J07", "category": "WRITING & DRAWING", "nameKo": "자크 허빈", "nameEn": "JACQUES HERBIN"},
  {"id": "L01-52", "code": "L01", "category": "WRITING & DRAWING", "nameKo": "지구화학", "nameEn": "GLOBE CHEMICAL"},
  {"id": "L03-53", "code": "L03", "category": "WRITING & DRAWING", "nameKo": "클립펜", "nameEn": "CLIPEN"},
  {"id": "M01-54", "code": "M01", "category": "COMMON", "nameKo": "네이버 라운지", "nameEn": "NAVER LOUNGE"},
  {"id": "M02-55", "code": "M02", "category": "COMMON", "nameKo": "리마스터드", "nameEn": "REMASTERED"},
  {"id": "M03-56", "code": "M03", "category": "COMMON", "nameKo": "레이어스투게더", "nameEn": "Layers Together"},
  {"id": "M04-57", "code": "M04", "category": "COMMON", "nameKo": "휴게공간", "nameEn": "LOUNGE"},
  {"id": "A06-58", "code": "A06", "category": "DAILY FINDS", "nameKo": "포인트오브뷰", "nameEn": "POV"},
  {"id": "B01-59", "code": "B01", "category": "DAILY FINDS", "nameKo": "포인트오브뷰 × 헬로키티", "nameEn": "POV × HELLO KITTY"},
  {"id": "B04-60", "code": "B04", "category": "DAILY FINDS", "nameKo": "덴스", "nameEn": "THENCE"},
  {"id": "B05-61", "code": "B05", "category": "DAILY FINDS", "nameKo": "밑미", "nameEn": "MEET ME"},
  {"id": "D03-62", "code": "D03", "category": "DAILY FINDS", "nameKo": "노빅딜", "nameEn": "NOBIGDEAL"},
  {"id": "E02-63", "code": "E02", "category": "DAILY FINDS", "nameKo": "공예가", "nameEn": "GONGYEGA"},
  {"id": "F04-64", "code": "F04", "category": "DAILY FINDS", "nameKo": "티에토아", "nameEn": "TIETOA"},
  {"id": "F05-65", "code": "F05", "category": "DAILY FINDS", "nameKo": "키티버니포니", "nameEn": "KITTY BUNNY PONY"},
  {"id": "G04-66", "code": "G04", "category": "DAILY FINDS", "nameKo": "오롤리데이", "nameEn": "OH, LOLLY DAY!"},
  {"id": "H03-67", "code": "H03", "category": "DAILY FINDS", "nameKo": "유어마인드", "nameEn": "YOUR-MIND"},
  {"id": "H04-68", "code": "H04", "category": "DAILY FINDS", "nameKo": "원모어백", "nameEn": "onemorebag"},
  {"id": "H08-69", "code": "H08", "category": "DAILY FINDS", "nameKo": "세일러즈", "nameEn": "SAILORS"},
  {"id": "I04-70", "code": "I04", "category": "DAILY FINDS", "nameKo": "오이뮤", "nameEn": "OIMU"},
  {"id": "J05-71", "code": "J05", "category": "DAILY FINDS", "nameKo": "하우키즈풀", "nameEn": "HOWkidsFUL"},
  {"id": "J06-72", "code": "J06", "category": "DAILY FINDS", "nameKo": "프란츠", "nameEn": "FRANZ"},
  {"id": "L07-73", "code": "L07", "category": "DAILY FINDS", "nameKo": "서커스보이밴드", "nameEn": "CIRCUS BOY BAND"},
  {"id": "L08-74", "code": "L08", "category": "DAILY FINDS", "nameKo": "트래블러스컴퍼니", "nameEn": "TRAVELER'S COMPANY"},
  {"id": "A01-75", "code": "A01", "category": "PAPER", "nameKo": "두성종이", "nameEn": "DOOSUNG PAPER"},
  {"id": "A12-76", "code": "A12", "category": "PAPER", "nameKo": "페이퍼테일러", "nameEn": "PAPER TAILOR"},
  {"id": "C04-77", "code": "C04", "category": "PAPER", "nameKo": "그린디자인웍스 공장", "nameEn": "GONGJANG"},
  {"id": "C05-78", "code": "C05", "category": "PAPER", "nameKo": "모스", "nameEn": "MOHS"},
  {"id": "C06-79", "code": "C06", "category": "PAPER", "nameKo": "오첵", "nameEn": "O-CHECK"},
  {"id": "C08-80", "code": "C08", "category": "PAPER", "nameKo": "한솔제지 × 어반북스", "nameEn": "HANSOL PAPER × URBANBOOKS"},
  {"id": "C10-81", "code": "C10", "category": "PAPER", "nameKo": "삼원페이퍼", "nameEn": "SAMWON PAPER"},
  {"id": "G02-82", "code": "G02", "category": "PAPER", "nameKo": "비온뒤", "nameEn": "BE ON D"},
  {"id": "G05-83", "code": "G05", "category": "PAPER", "nameKo": "웬아이워즈영", "nameEn": "WHENIWASYOUNG"},
  {"id": "H01-84", "code": "H01", "category": "PAPER", "nameKo": "가위", "nameEn": "KAWI"},
  {"id": "I01-85", "code": "I01", "category": "PAPER", "nameKo": "트롤스페이퍼", "nameEn": "TROLLS PAPER"},
  {"id": "I02-86", "code": "I02", "category": "PAPER", "nameKo": "양지사", "nameEn": "YANGJISA"},
  {"id": "I03-87", "code": "I03", "category": "PAPER", "nameKo": "소소문구", "nameEn": "SOSOMOONGOO"},
  {"id": "I05-88", "code": "I05", "category": "PAPER", "nameKo": "페이퍼플레이트", "nameEn": "PAPER PLATE"},
  {"id": "I07-89", "code": "I07", "category": "PAPER", "nameKo": "글월", "nameEn": "GEULWOL"},
  {"id": "J02-90", "code": "J02", "category": "PAPER", "nameKo": "컴포지션 스튜디오", "nameEn": "COMPOSITION STUDIO"},
  {"id": "J04-91", "code": "J04", "category": "PAPER", "nameKo": "몰스킨", "nameEn": "MOLESKINE"},
  {"id": "J11-92", "code": "J11", "category": "PAPER", "nameKo": "로디아", "nameEn": "RHODIA"},
  {"id": "L02-93", "code": "L02", "category": "PAPER", "nameKo": "페이퍼리안", "nameEn": "PAPERIAN"},
  {"id": "L02-94", "code": "L02", "category": "PAPER", "nameKo": "더컴플리티스트", "nameEn": "THE COMPLETIST"},
  {"id": "B08-95", "code": "B08", "category": "OFFICE & DESK", "nameKo": "펜코", "nameEn": "PENCO®"},
  {"id": "C01-96", "code": "C01", "category": "OFFICE & DESK", "nameKo": "로지텍", "nameEn": "LOGITECH"},
  {"id": "C11-97", "code": "C11", "category": "OFFICE & DESK", "nameKo": "고쿠요", "nameEn": "KOKUYO"},
  {"id": "D01/02-98", "code": "D01/02", "category": "OFFICE & DESK", "nameKo": "일룸", "nameEn": "ILOOM"},
  {"id": "D05-99", "code": "D05", "category": "OFFICE & DESK", "nameKo": "워크인프로그래스", "nameEn": "WIP"},
  {"id": "E06-100", "code": "E06", "category": "OFFICE & DESK", "nameKo": "바이하츠", "nameEn": "BUYHEARTS"},
  {"id": "F02-101", "code": "F02", "category": "OFFICE & DESK", "nameKo": "매거진C", "nameEn": "MAGAZINE C"},
  {"id": "G01-102", "code": "G01", "category": "OFFICE & DESK", "nameKo": "낼나", "nameEn": "NELNA"},
  {"id": "I06-103", "code": "I06", "category": "OFFICE & DESK", "nameKo": "모트모트", "nameEn": "MOTEMOTE"},
  {"id": "J08-104", "code": "J08", "category": "OFFICE & DESK", "nameKo": "프러스", "nameEn": "PLUS"},
  {"id": "L02-105", "code": "L02", "category": "OFFICE & DESK", "nameKo": "크래프트디자인테크놀로지", "nameEn": "CRAFT DESIGN TECHNOLOGY"},
  {"id": "L05-106", "code": "L05", "category": "OFFICE & DESK", "nameKo": "산로", "nameEn": "SANRO"},
  {"id": "K01-107", "code": "K01", "category": "KIOSK", "nameKo": "마크스", "nameEn": "MARK'S"},
  {"id": "K02-108", "code": "K02", "category": "KIOSK", "nameKo": "우이", "nameEn": "OUIE"},
  {"id": "K03-109", "code": "K03", "category": "KIOSK", "nameKo": "민도비또", "nameEn": "MINDOBITTO"},
  {"id": "K04-110", "code": "K04", "category": "KIOSK", "nameKo": "화랑", "nameEn": "HWARANG"},
  {"id": "K05-111", "code": "K05", "category": "KIOSK", "nameKo": "동키콩테", "nameEn": "DONKY CONTÉ"},
  {"id": "K06-112", "code": "K06", "category": "KIOSK", "nameKo": "안테리크", "nameEn": "ANTERIQUE"},
  {"id": "K07-113", "code": "K07", "category": "KIOSK", "nameKo": "수이과슈", "nameEn": "SUI GOUACHE"},
  {"id": "K08-114", "code": "K08", "category": "KIOSK", "nameKo": "냥냥빔", "nameEn": "MEWMEWBEAM"},
  {"id": "K09-115", "code": "K09", "category": "KIOSK", "nameKo": "노용원 혁필화", "nameEn": "NOH YONG-WON"}
]
```

> 출처: 공식 부스 배치도(INVENTARIO 2026) 범례를 전수 판독·검수한 목록. 브랜드 자체 표기(낼나/프러스/워크인프로그래스 등)와 중복 부스코드(E05·H06·L02, 멀티브랜드 부스)는 원문 그대로 유지함.
