---
layout: post
title: Flutter Study Week3
subtitle: Lec 15~19
categories: Flutter
tags: Flutter APP
sidebar: []
---



[강의 - 완전 초보용 플러터 강좌](https://www.youtube.com/watch?v=AdYRASHRKwE&list=PLQt_pzi-LLfpcRFhWMywTePfZ2aPapvyl)

### Drawer menu

[실습구조]

- Drawer → ListView
  - → UserAccountDrawerHeader
  - → ListTile
- onPressed: 주로 버튼에 사용
- onTab: 동작에 반응하는 이벤트에 사용

### BuildContext

- widget tree에서 현재 widget의 위치를 알 수 있는 정보
  - Scaffold Widget에 context를 넣어서 return
- 이 BuildContext는 stateless 위젯이나 state 빌드 메서드에 의해서 리턴된 위젯의 부모가 됨

### ScaffoldMessanger

- 자손 scaffold를 위해 snackbar를 관리해줌