# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

개인용 할일 관리 앱. 코딩을 모르는 사용자가 매일 10~20개의 할일을 관리하기 위한 도구.

## 실행 방법

빌드·설치·서버 없음. `index.html` 파일을 브라우저에서 더블클릭해 열면 바로 동작한다.
변경 후에는 브라우저 강력 새로고침(`Cmd + Shift + R`)으로 확인한다.

## 제약 (반드시 지킬 것)

- **단일 파일**: HTML/CSS/JavaScript를 `index.html` 한 파일 안에 모두 둔다. 파일을 분리하지 않는다.
- **외부 의존성 금지**: 라이브러리·프레임워크·CDN·빌드도구를 쓰지 않는다. 순수 HTML/CSS/JS만 사용.
- **오프라인**: 인터넷 연결 없이 더블클릭만으로 열려야 한다.
- 사용자는 비개발자이므로, 코드에는 동작 방식을 설명하는 쉬운 한국어 주석을 단다.

## 구조 (index.html)

`<style>` / `<body>` / `<script>` 세 부분으로 구성. 화면은 위에서 아래로 5개 영역:
1. 상단(제목 + 오늘 날짜 자동 표시)  2. 진행률  3. 입력  4. 필터  5. 할일 목록

### 데이터 모델
- 모든 할일은 전역 배열 `todos`에 보관. 각 항목은 `{ id, title, category, done }`.
- `nextId`로 고유 id를 발급(겹침 방지). 새 할일은 `unshift`로 맨 앞에 추가.
- 카테고리: 작업 / 공부 / 개인 / 업무 / 기술 / 지원. 각 카테고리는 `badge.<카테고리명>` CSS 클래스로 배지 색이 결정된다. **카테고리를 추가·변경하면 세 곳을 모두 고쳐야 한다**: 입력 `<select>`, 필터 버튼, `.badge.<이름>` CSS 색상.

### 렌더링 원칙
- 데이터가 바뀌면 항상 `render()`를 호출해 목록 전체를 다시 그린다(데이터와 화면 일치).
- `render()`는 `updateProgress()`(진행률 계산)와 `getFilteredTodos()`(현재 필터 적용)를 거친다.
- 진행률은 필터와 무관하게 **전체 `todos` 기준**으로 계산한다.

### 저장 (localStorage)
- `saveTodos()` / `loadTodos()`가 `STORAGE_KEY = "myTodos"`로 저장·복원. `JSON.stringify`/`JSON.parse` 사용.
- 데이터를 바꾸는 모든 함수(`addTodo`, `toggleDone`, `editTodo`, `deleteTodo`)는 `render()` 직전에 `saveTodos()`를 호출해야 한다. **새 변경 함수를 추가하면 이 패턴을 지킬 것.**
- 데이터는 파일이 아니라 브라우저별 localStorage에 남는다(브라우저를 바꾸면 비어 보임).

### 반응형
- 480px 이하 화면용 `@media` 쿼리로 입력 영역을 세로로 쌓고 버튼을 키운다.
