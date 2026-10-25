# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 프로젝트 개요

개발자 본인을 소개하는 싱글 페이지 웹 이력서다. 현재는 `ROADMAP.md`만 있고 소스 코드는 아직 없다(1단계 준비 단계). 작업 순서와 완료 기준은 `ROADMAP.md`를 따른다.

## 명령어

빌드, 린트, 테스트 설정이 아직 없다. Tailwind CSS를 CDN으로 쓸지 CLI 빌드로 쓸지는 `ROADMAP.md` 1단계에서 정하기로 되어 있다. 설정이 생기면 이 섹션을 갱신한다.

## 아키텍처 (계획)

`ROADMAP.md`의 예정 구조를 기준으로 한다.

- **단일 페이지**: `index.html` 하나에 모든 섹션을 둔다. 섹션 순서는 헤더, 소개, 기술 스택, 경력, 프로젝트, 학력 및 자격증, 연락처 및 링크, 푸터다. 시맨틱 태그(`header`, `main`, `section`, `footer`)를 쓴다.
- **스타일**: Tailwind CSS 유틸리티 클래스를 기본으로 하고, 추가 스타일은 `css/style.css`에 둔다. 다크 모드는 `dark:` 클래스로 정의한다.
- **인터랙션**: `js/main.js` 하나에서 다크/라이트 토글(`localStorage`에 설정 저장), 모바일 햄버거 메뉴, 섹션 부드러운 스크롤, 스크롤 위치에 따른 메뉴 강조를 처리한다.
- **자산**: 이미지, 아이콘, 프로필 사진은 `assets/`에 둔다.

## 언어 및 커뮤니케이션 규칙

- **기본 응답 언어**: 한국어
- **코드 주석**: 한국어로 작성
- **커밋 메시지**: 한국어로 작성
- **문서화**: 한국어로 작성
- **변수명/함수명**: 영어 (코드 표준 준수)
