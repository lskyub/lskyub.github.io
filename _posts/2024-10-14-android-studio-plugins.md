---
layout: post
title:  "Android Studio Plugins 추천"
date:   2024-10-14 09:00:00 +0900
categories: android
tags: [android]
---

Android Studio는 전 세계 개발자들이 애플리케이션을 구축하는 데 사용하는 강력한 통합 개발 환경(IDE)입니다. 하지만 기본적으로 제공되는 기능 외에 다양한 플러그인을 통해 개발 효율성을 높일 수 있습니다. 이번 포스트에서는 Android Studio에서 유용하게 사용할 수 있는 추천 플러그인들을 소개하겠습니다.

## 요약
|플러그인 | 설명 
|-|:-
| ADB Idea | ADB 명령어 처리                           
| StringManipulation | 문자열 관련 기능을 자동화                          
| CodeGlance | 코드뷰                             
| Fast-Scrolling | 스크롤               

## 1. ADB Idea
Android Studio의 Find Actions에 ADB commands의 추가 해주는 플로그인으로 아래 명령어를 추가 하여 쉽게 사용이 가능하다 
- ADB Uninstall App : ADB 앱 제거
- ADB Kill App : ADB 킬 앱
- ADB Start App : ADB 시작 앱
- ADB Restart App : ADB 앱 재시작
- ADB Clear App Data : ADB 앱 데이터 지우기
- ADB Clear App Data and Restart : ADB 앱 데이터 지우기 및 다시 시작
- ADB Start App With Debugger : 디버거를 사용하여 ADB 시작 앱
- ADB Restart App With Debugger : 디버거를 사용하여 ADB 앱 다시 시작
- ADB Grant/Revoke Permissions : ADB 권한 부여/철회
- ADB Enable/Disable Wi-Fi : ADB Wi-Fi 활성화/비활성화
- ADB Enable/Disable Mobile Data : ADB 모바일 데이터 활성화/비활성화
> osx: cmd+shift+a, windows/linux: ctrl+shift+a

## 2. StringManipulation
Android Studio에서 문자열 작업을 보다 효율적으로 수행할 수 있도록 도와주는 유용한 도구입니다. 이 플러그인을 이용하면 프로그래머가 자주 사용하는 문자열 관련 기능을 자동화하고 간편하게 사용할 수 있습니다
- 문자열 변환 : 문자열을 대문자, 소문자, 또는 제목 형식으로 변환하는 기능을 제공합니다. 코드에서 문자열을 더욱 쉽게 관리할 수 있습니다.
- 구분자 변경 : 문자열의 구분자를 변경하는 기능을 통해 Java나 Kotlin 코드의 스타일을 일관되게 유지할 수 있습니다.
- 문자열 연결 : 여러 문자열을 하나로 결합할 수 있는 기능을 제공합니다. 예를 들어, 여러 줄의 문자열을 쉽게 연결할 수 있습니다.
- 필터링 및 정리 : 불필요한 공백이나 특수 문자를 제거하는 등의 문자열 정리 기능도 포함되어 있어, 데이터를 정렬하고 정리하는 작업을 손쉽게 할 수 있습니다.
- 정규 표현식 지원 : 정규 표현식을 사용하여 복잡한 문자열 검색 및 대체 작업을 수행할 수 있습니다.
- ASCII 변환 : 문자열을 ASCII 코드로 변환하거나 ASCII 코드를 문자열로 변환하는 기능을 제공합니다.

## 3. CodeGlance
Android Studio에서 Subrime에서 볼 수 있는 것과 유사한 확대 개요 또는 미니맵을 표시하는 도구 입니다. 미니맵을 사용하면 빠르게 스크롤하여 코드 섹션으로 바로 이동할 수 있습니다. 
- Hide original scrollbar : 원래 스크롤 막대 숨기기
- Right click to quick config : 마우스 오른쪽 버튼을 클릭하여 빠르게 구성합니다
- Support markup highlights : 마크업 하이라이트를 지원합니다
- Support error stripes highlights : 오류 줄무늬 강조 표시를 지원합니다
- Support Vcs line highlights : VCS 라인 하이라이트를 지원합니다
- Support caret line highlights : 지원 캐럿 라인 하이라이트
- Support language ColorScheme : 지원 언어 색상 체계
- Quick view code on Glance : Glance에서 빠른 보기 코드
- Automatically calculate width in splitter mode : 스플리터 모드에서 자동으로 너비를 계산합니다
> osx: ctrl+shift+g : Show/Hide

## 4. Fast-Scrolling
Android Studio에서 스크롤을 할때 스크롤 범위를 늘려주는 도구