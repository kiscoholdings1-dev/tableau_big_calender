# tableau_big_calender

Tableau Dashboard Extension 기반 날짜 선택 UI 확장 프로그램.

기본 Tableau 날짜 선택 UI보다 더 큰 달력 형태로 날짜를 선택하고, 선택 결과를 Tableau 파라미터에 반영하는 목적의 확장 프로그램이다.

배포 URL:
`https://kiscoholdings1-dev.github.io/tableau_big_calender/`

매니페스트:
[`docs/calender.trex`](/c:/dev/tableau_big_calender/docs/calender.trex)

## 목적

- Tableau 대시보드 내 기본 날짜 UI를 더 보기 쉽게 개선
- 단일 날짜 / 기간 조회를 더 쉽게 변경
- 자주 쓰는 기간은 빠른조회 버튼으로 즉시 적용
- 고정된 확장 프로그램 영역 안에서도 한 달 전체 일자를 한눈에 보이도록 최적화

## 현재 기능

### 1. 기간 조회 모드

- 시작날짜 / 종료날짜를 각각 선택 가능
- 달력에서 기간(range) 선택 가능
- 적용 버튼으로 파라미터 반영
- 빠른조회 지원
  - 금일
  - 전일
  - 금주 누계
  - 당월 누계
  - 연 누계

### 2. 단일 날짜 모드

- 조회날짜 1개만 선택
- 대시보드 사용 방식상, 선택한 날짜가 속한 월 기준으로 데이터를 조회하는 용도
- 빠른조회 버튼 동작이 기간 모드와 다름
  - 당월: 오늘 날짜 선택
  - 전월: 전월 말일 선택
- `금주 누계`, `당월 누계`, `연 누계` 버튼은 숨김

### 3. 빠른조회

- 버튼 클릭 시 바로 날짜를 계산해 pending 상태로 반영
- 이후 적용 버튼으로 Tableau 파라미터 변경
- 모드별 의미가 다를 수 있으므로 `single` / `range` 분기 처리됨

### 4. 설정 기능

작성 모드(authoring mode)에서만 설정 버튼이 보인다.

설정 가능 항목:
- 조회 타입
  - `range`
  - `single`
- 시작 파라미터
- 종료 파라미터
- 표시 포맷

표시 포맷 기본값:
- `Y. n. j`
- 화면 표시 예: `2026. 4. 3.`

이전 포맷값(`Y-m-d`, `Y. m. d` 등)이 저장돼 있어도 현재 포맷으로 자동 보정한다.

## UI/UX 반영 내용

최근 반영된 개선 사항:

- 달력 영역을 고정 프레임 안에 맞도록 재압축
- 한 달 6주 전체가 스크롤 없이 보이도록 조정
- 월 헤더 / 요일 / 일자 가독성 개선
- 달력 하단 여백 축소 및 날짜 셀 영역 확대
- 상단 날짜영역과 버튼영역 간격 축소
- 상단 영역을 왼쪽 기준 흐름으로 compact하게 조정
- single 모드 빠른조회 라벨/동작 분기
  - `금일 -> 당월`
  - `전일 -> 전월`

## 동작 방식

### 파라미터 연동

- Tableau dashboard의 파라미터 목록을 읽음
- 날짜형 파라미터만 설정 후보로 사용
- 선택된 파라미터 값 변경 시 UI도 다시 동기화
- `ParameterChanged` 이벤트를 구독해서 외부 변경도 반영

### 날짜 표시

- 내부적으로는 `Date`, 문자열, 숫자형 날짜 값 등을 해석
- UI 표시는 Flatpickr 포맷 기준으로 출력
- 현재 기본 출력은 `yyyy. m. d` 형태

### 적용 흐름

- 달력 또는 빠른조회에서 날짜 선택
- 상단 날짜 표시가 갱신됨
- 적용 버튼 클릭 시 Tableau 파라미터에 최종 반영

## 파일 구조

- [`docs/index.html`](/c:/dev/tableau_big_calender/docs/index.html)
  - 메인 확장 UI
- [`docs/main.js`](/c:/dev/tableau_big_calender/docs/main.js)
  - 핵심 동작 로직
- [`docs/styles.css`](/c:/dev/tableau_big_calender/docs/styles.css)
  - UI 스타일
- [`docs/config.html`](/c:/dev/tableau_big_calender/docs/config.html)
  - 설정 다이얼로그 화면
- [`docs/config.js`](/c:/dev/tableau_big_calender/docs/config.js)
  - 설정 저장 로직
- [`docs/calender.trex`](/c:/dev/tableau_big_calender/docs/calender.trex)
  - Tableau Extension manifest
- [`docs/lib/flatpickr.min.js`](/c:/dev/tableau_big_calender/docs/lib/flatpickr.min.js)
  - 달력 라이브러리
- [`docs/lib/tableau.extensions.1.latest.min.js`](/c:/dev/tableau_big_calender/docs/lib/tableau.extensions.1.latest.min.js)
  - Tableau Extensions API

## 배포 방식

- GitHub Pages 정적 배포
- `docs/` 하위 파일이 실제 서비스 자원
- Tableau에서는 `.trex` 매니페스트를 통해 확장 프로그램을 불러옴
- Tableau Cloud에서 사용하려면 확장 프로그램 URL 등록 또는 접근 가능한 배포 URL 구성이 필요함

## 재사용 시 유의사항

- 다른 대시보드에 재사용할 경우, 확장 프로그램 표시 영역 크기에 맞춰 [`docs/styles.css`](/c:/dev/tableau_big_calender/docs/styles.css)의 상단 바/달력 영역 크기를 조정하는 것을 권장
- 특히 달력은 대시보드 내 실제 배치 높이와 폭에 따라 가독성이 크게 달라지므로, `rangeBar`, `quickHost`, `calHost` 관련 스타일을 함께 조정해야 함
