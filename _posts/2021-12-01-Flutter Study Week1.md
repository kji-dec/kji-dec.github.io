---
layout: post
title: Flutter Study Week1
subtitle: Lec 1~8
categories: Flutter
tags: Flutter APP
sidebar: []
---



[강의 - 완전 초보용 플러터 강좌](https://www.youtube.com/watch?v=AdYRASHRKwE&list=PLQt_pzi-LLfpcRFhWMywTePfZ2aPapvyl)

### **설치 및 환경변수 설정**

**설치**

- flutter.dev에서 zip 파일 다운로드 및 압축해제
- Android Studio 설치 및 emulator 설치

**환경변수 설정**

- 시스템변수 - Path에서 새로만들기를 눌러 flutter\bin 파일의 위치를 넣어줌
- cmd에서 flutter 입력 시 잘 작동되는 것을 확인할 수 있음

![image](https://user-images.githubusercontent.com/71377968/144205773-8450e27a-a005-4fb6-8be6-c181c2a08343.png)

### Widget

: 독립적으로 실행되는 작은 프로그램 → 그래픽/데이터 처리하는 함수를 가지고 있음

**Flutter에서는?**

UI를 만들고 구성하는 모든 기본 단위 요소 + 눈에 보이지 않는 레이아웃을 정의하는 요소 = 모든것!

1. Stateless Widget
   1. 정적인 위젯 → 움직이지 않는 위젯
   2. 스크린상에만 존재하며, 아무것도 하지 않음
   3. 실시간 데이터 저장 X
   4. 모양 변화와 관련한 value X
2. Stateful Widget
   1. 동적인 위젯 → 상태 변화가 있는 위젯
   2. 사용자의 interaction에 따라서 모양이 바뀜
   3. 데이터를 받게 되었을 때 모양이 바뀜 (ex. text box)
3. Inherited Widget

**Widget tree**

1. Widget은 tree구조로 정리 가능
2. 한 Widget 내에 다른 widget이 얼마든지 포함될 수 있음
3. Widget은 부모 위젯과 자식 위젯으로 구성
4. Parent widget = widget container

강의 예시)

![image](https://user-images.githubusercontent.com/71377968/144205699-5c352623-86ca-4c48-87f1-dd30e2efacec.png)

*이 중 Scaffold widget은 앱 화면과 기능을 구성하기 위한 빈 페이지를 제공해주는 위젯임*

### Flutter project 구성

1. pubspec.yaml: 프로젝트의 메타데이터 정의 및 관리
2. android/ios: 각 os에 맞게 배포하기 위한 정보를 담은 것
3. test: 개발 시 테스트 코드 작성 가능
4. lib: 앱 제작 작업의 대부분 차지