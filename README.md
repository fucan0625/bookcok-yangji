# BOOK-COK

NFC 태그로 시작하는 독서 집중 웹앱. 양지고등학교 4차시 수업 프로젝트.

**배포 주소** — https://bookcok-yangji.web.app

---

## 이게 어떤 프로젝트인가

AI가 만든 프로토타입을 받아서, **동작하지 않는 부분을 찾아 고쳐나간 기록**이다.
커밋 하나가 고친 것 하나이므로 `git log --oneline` 자체가 작업 기록이자 발표 자료다.

```
[NFC 태그]  →  [폰 자동화]            →  [BOOK-COK 웹앱]
  물리적        방해금지 ON + 주소 열기     타이머·기록·백색소음
                (OS만 할 수 있는 일)        (이 저장소)
```

웹페이지는 방해금지 모드를 켤 수 없다. 그래서 켜는 일은 폰 자동화에 맡기고,
웹앱은 URL로 "자동화를 거쳐 들어왔다"는 사실만 전달받는다.

## 파일

| 파일 | 내용 |
|---|---|
| `public/index.html` | 앱 전체 (HTML+CSS+JS 한 파일) |
| `public/manifest.json`, `icon-*.png` | 홈 화면 추가용 |
| `firebase.json` | 배포 설정 — `public/` 안의 것만 올린다 |
| [학생-설명-가이드.md](학생-설명-가이드.md) | **아이들에게 전 과정을 설명하는 대본** |
| [고친-버그-목록.md](고친-버그-목록.md) | 발표용. 찾아서 고친 것 17개 |
| [수업-가이드-보강.md](수업-가이드-보강.md) | 차시별 배치 |
| [NFC-처음부터.md](NFC-처음부터.md) | NFC를 한 번도 안 다뤄본 사람용 |
| [폰-자동화-설정.md](폰-자동화-설정.md) | 단축어·매크로 설정 절차 |

## 진입 주소

| 주소 | 언제 |
|---|---|
| `/?src=nfc&dnd=on` | `독서 시작` 단축어가 여는 주소 (방해금지 켜짐) |
| `/?done=1` | `독서 끝` 단축어가 여는 주소 (방해금지 꺼짐) |
| `/?src=nfc&book=제목` | 책마다 태그를 붙일 때 (방해금지는 안 켜짐) |

같은 태그를 두 번 대면 앱이 독서 세션을 보고 시작/종료를 스스로 판단한다.
종료일 때는 앱이 `독서 끝` 단축어를 직접 호출해 방해금지를 끈다.

---

## 다른 컴퓨터에서 이어서 작업하기

```bash
git clone https://github.com/fucan0625/bookcok.git
cd bookcok
```

빌드 도구가 없다. `public/index.html` 하나를 고치면 끝이다.

### 로컬에서 열어보기

```bash
python -m http.server 8000 --bind 0.0.0.0 -d public
```

`http://localhost:8000` 또는 같은 와이파이의 폰에서 `http://<노트북IP>:8000`.
HTTPS가 필요 없다 — URL 파라미터·소리·localStorage 모두 http에서 동작한다.

### 배포

```bash
npm i -g firebase-tools     # 없으면
firebase login              # fccad01@gmail.com
firebase deploy --only hosting --project bookcok-yangji
```

배포 후 **파일 개수를 확인할 것.** `found 6 files`가 정상이다
(index.html + manifest.json + 아이콘 4개). 숫자가 크면 뭔가 잘못 올라가는 중이다.

### 고쳤는데 폰에서 안 바뀌면

`index.html`은 `no-cache`로 배포되지만 이미 캐시된 사본이 남아 있을 수 있다.
탭을 닫았다 열거나 주소 뒤에 `?v=2`를 붙인다.

---

## 손으로 확인하기 어려운 것 테스트하기

브라우저 없이 `public/index.html`의 스크립트를 실행해보는 방법.
가짜 DOM을 만들어 `new Function(js)()`로 돌리면 된다.

**주의**: Node에는 `navigator`가 내장돼 있어 `global.navigator = {...}`가 **무시된다.**
아이폰인 척 테스트하려면 이렇게 해야 한다.

```js
Object.defineProperty(global, 'navigator',
  { value: { userAgent: 'iPhone' }, configurable: true });
```

이걸 몰라서 한동안 아이폰 경로를 테스트한다고 믿으면서 안드로이드 경로를 검사했다.

## 아직 안 된 것

- [ ] `독서 시작` / `독서 끝` 단축어의 iCloud 공유 링크
      → `public/index.html`의 `SHORTCUT_LINKS`에 넣으면 설정이 버튼 한 번으로 끝난다
- [ ] NFC 스티커 실물 테스트 (읽히는 위치, 금속 위 여부)
- [ ] 안드로이드 실기기에서 MacroDroid 자동화 확인
