# assign05 - JavaScript DOM / Array CRUD

22400315 박형찬

- GitHub: https://github.com/2026-2-OSS/assign05-c02-22400315
- Vercel 배포 URL: (배포 후 기입)

## Deployment

Vercel에 배포한 뒤 위 URL을 클릭하면 `index.html`이 바로 뜨고, 거기서 두 페이지로 들어갈 수 있습니다.

## Key Learning

1. 서버나 DB 없이도 JavaScript 배열(`movies`) 하나로 데이터를 들고 있다가, 배열이 바뀔 때마다 `render()`를 다시 불러서 화면을 통째로 새로 그리는 방식으로 CRUD를 흉내 낼 수 있다는 걸 알았다.
2. `find()`는 배열에서 조건에 맞는 객체 하나(참조)를 찾아 그 자리에서 값을 바꾸는 데 쓰고, `filter()`는 조건에 맞는 것들만 남긴 새 배열을 만들어서 삭제를 구현하는 데 쓴다는 차이를 체감했다.
3. `li.innerText = value`로 텍스트를 넣은 다음 버튼을 `appendChild`로 추가하면 그 전에 넣었던 텍스트가 사라진다. 텍스트는 `span`에 따로 넣고 버튼은 별도 요소로 만들어서 둘 다 `li`에 붙여야 한다.

## CRUD Service

- **주제**: 영화 관리 (Movie Management)
- **데이터 Field (6개)**: `id`, `title`(제목), `director`(감독), `genre`(장르), `year`(개봉년도), `rating`(평점, 0~10)
- **Create**: `crud.html`의 Form에 입력 → Validation 통과 시 `movies.push()`로 배열에 추가 → `render()` 호출 → Form 초기화
- **Read**: 페이지 로드 시 `window.onload`에서 `render()` 호출, `movies.forEach()`로 각 영화를 `<tr>`로 만들어 표에 출력
- **Update**: 표의 [수정] 버튼 → `startEdit(id)`가 `find()`로 해당 영화를 찾아 Form에 값 채움, 버튼 글자를 "저장"으로 바꿈 → 다시 제출하면 `editingId`로 추적해둔 객체의 값을 직접 수정 → `render()`
- **Delete**: 표의 [삭제] 버튼 → `confirm("삭제하시겠습니까?")` → 확인 시 `filter()`로 해당 id를 뺀 새 배열로 교체 → `render()`

## JavaScript

- `querySelector()` / `getElementById()` — Form, input, table body 등 요소 찾기
- `addEventListener("submit"/"click", ...)` — Form 제출, 추가/수정/삭제 버튼 클릭 처리
- `createElement()` / `appendChild()` / `remove()` — `js_dynamic.html`에서 목록 항목(li, span, button)을 동적으로 만들고 붙이고 지움
- `Array.push()` — Create 시 배열 끝에 새 영화 추가
- `Array.find()` — Update 시 id로 해당 객체를 찾아 값을 바꿈
- `Array.filter()` — Delete 시 특정 id를 제외한 새 배열 생성
- `Array.forEach()` — render()에서 배열 전체를 순회하며 `<tr>` 생성
- `render()` — 배열 → DOM으로 화면을 다시 그리는 함수. Create/Update/Delete 뒤에 매번 호출

## AI / Search Usage

- Claude Code를 사용해서 전체 구조를 함께 짰습니다. `find()`로 객체를 찾은 뒤 그 참조에 값을 바로 대입하면 배열 안의 원본 객체가 바뀐다는 점, `filter()`는 원본을 바꾸지 않고 새 배열을 반환한다는 점을 Claude 설명을 보고 직접 콘솔에서 테스트해보며 이해했습니다.
- `li.innerText`로 텍스트를 넣은 뒤 버튼을 추가하면 버튼까지 지워지는 문제도 Claude가 원인(innerText가 기존 자식 요소를 통째로 덮어씀)을 짚어줘서, span과 button을 분리하는 방식으로 고쳤습니다.
- 코드를 그대로 복붙하지 않고 각 함수(`render`, `startEdit`, `deleteMovie`, `validateMovie`)가 어떤 순서로 호출되는지 직접 콘솔(`javascript_tool`로 add→edit→delete 순서 테스트)에 찍어보며 확인했습니다.

## Problem & Solution

- **문제**: Update 구현 초반에 수정 후 저장을 누르면 기존 영화를 수정하는 대신 새 영화가 추가됐습니다.
  **해결**: `editingId` 변수를 두어 "지금 몇 번 id를 수정 중인지"를 기억하게 하고, 제출 시 `editingId`가 있으면 `find()`로 찾은 객체 값만 바꾸고, 없으면 `push()`하도록 분기했습니다.
- **문제**: `js_dynamic.html`에서 `li.innerText`로 글자를 넣은 다음 삭제 버튼을 `appendChild`로 붙이면 버튼을 누르는 순간 텍스트 전체가 사라지는 현상이 있었습니다.
  **해결**: 텍스트용 `span`과 버튼을 따로 만들어서 각각 `li`에 붙이는 방식으로 바꿨습니다.

## Reflection

지난 과제(assign03)에서는 페이지마다 새로고침되고 `localStorage`로 데이터를 유지했는데, 이번엔 배열 하나와 `render()` 함수만으로 새로고침 없이 화면이 즉시 바뀌는 걸 보면서 "실제 서비스들이 쓰는 동적 화면 갱신"의 기본 원리를 조금 알게 됐습니다. 데이터가 많아지면 매번 테이블 전체를 다시 그리는 지금 방식이 느려질 수도 있을 것 같은데, 실제 프레임워크(React 등)는 이걸 어떻게 더 효율적으로 처리하는지 궁금합니다.

## 파일 구성

- `index.html` — 페이지 링크
- `js_dynamic.html` — JavaScript DOM 연습 (목록 추가/삭제)
- `crud.html` — 영화 관리 CRUD (Create/Read/Update/Delete + Validation)
- `style.css` — 공통 스타일
