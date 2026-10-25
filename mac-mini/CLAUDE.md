# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 프로젝트 위치

실제 앱 코드는 `bucket-list-main/` 아래에 있습니다. 이 저장소(`claude-code-mastery`)는 git 저장소가 아니며, 빌드 설정이나 패키지 파일이 없습니다.

## 실행 방법

빌드, 린트, 테스트 설정이 없습니다. 아래 방법으로 직접 실행합니다.

- `bucket-list-main/index.html`을 브라우저에서 직접 엽니다.
- 또는 정적 서버를 띄웁니다: `cd bucket-list-main && python -m http.server 8000` 후 `http://localhost:8000` 접속

`index.html`은 Tailwind CSS를 CDN(`https://cdn.tailwindcss.com`)에서 불러오므로 실행하려면 인터넷 연결이 필요합니다.

## 아키텍처 (큰 그림)

순수 프론트엔드 Vanilla JS 앱입니다. 프레임워크, 번들러, 서버가 없습니다. 데이터는 브라우저 `localStorage`의 `bucketList` 키에 JSON 배열로 저장됩니다.

**두 계층으로 나뉩니다.**

- `js/storage.js`의 `BucketStorage` 객체는 데이터 계층입니다. CRUD, 완료 토글, 통계, 필터링을 담당하며 DOM을 전혀 건드리지 않습니다. 모든 메서드는 `load()`로 전체 배열을 읽고, 수정한 뒤 `save()`로 전체 배열을 다시 씁니다.
- `js/app.js`의 `BucketListApp` 클래스는 UI 계층입니다. 이벤트를 연결하고, `render()`에서 통계 영역과 목록 영역을 매번 다시 그립니다. 목록은 `innerHTML` 문자열로 렌더링합니다.

**흐름:** 사용자 이벤트 → `BucketListApp.handleXxx()` → `BucketStorage.xxx()`(localStorage 변경) → `render()`(전체 재렌더링)

**전역 상태**
- `app.js` 마지막의 `let app`이 `DOMContentLoaded` 시점에 인스턴스를 담습니다.
- 목록 항목의 버튼은 `onclick="app.handleToggle('...')"` 같은 인라인 핸들러를 씁니다. 따라서 `app` 전역 이름을 바꾸면 이 HTML 문자열도 함께 고쳐야 합니다.

**스크립트 로드 순서가 중요합니다.** `index.html`에서 `storage.js`가 `app.js`보다 먼저 로드되어야 합니다. `app.js`는 `BucketStorage`를 전역에서 참조합니다.

**스타일 구조**
- 레이아웃과 색상은 Tailwind 유틸리티 클래스로 `index.html`과 `app.js`의 템플릿 문자열에 직접 씁니다.
- `css/styles.css`는 Tailwind로 표현하기 어려운 부분만 담당합니다. 필터 버튼 활성 상태, 항목 등장 애니메이션, 640px 이하 반응형, 다크 모드 오버라이드(`prefers-color-scheme`)가 여기에 있습니다.
- 다크 모드는 `.bg-white`, `.text-gray-800` 같은 Tailwind 클래스 이름을 직접 덮어씁니다. 해당 클래스를 새로 쓰면 다크 모드에서 어긋날 수 있습니다.

**데이터 모델** (`storage.js` `addItem` 참고)
`{ id: 문자열(Date.now 기반), title, completed, createdAt(ISO), completedAt(ISO 또는 null) }`. 새 항목은 배열 맨 앞(`unshift`)에 들어갑니다.

## 수정할 때 주의할 점

- 목록 항목 HTML은 `createBucketItemHTML()`에서 템플릿 문자열로 만듭니다. 사용자 입력인 제목은 `escapeHtml()`을 거쳐야 합니다. 다만 현재 `escapeHtml`은 `"`와 `'`를 이스케이프하지 않으므로, 인라인 `onclick` 속성에 제목을 넣는 부분은 깨질 수 있습니다.
- `addItem`의 ID는 `Date.now().toString()`입니다. 같은 밀리초에 두 항목이 생성되면 충돌할 수 있습니다.
- `README.md`는 `assets/` 폴더가 있다고 적혀 있지만 실제로는 없습니다. 폴더 이름도 `bucket-list`가 아니라 `bucket-list-main`입니다. README의 "향후 개선 사항" 목록은 아직 구현되지 않은 기능입니다.
- 문서와 주석은 한국어로 작성합니다. 기존 JSDoc 스타일을 따릅니다.
