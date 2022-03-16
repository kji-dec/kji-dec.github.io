---
layout: post
title: Database Study Week1
subtitle: 섹션 0, 1 - 데이터베이스 개요 & MySQL 설치
categories: Database
tags: Database RDBMS MySQL
sidebar: []
---



[강의 링크](https://www.inflearn.com/course/database-2-mysql-강좌/dashboard)

<br>

### 섹션0 - 데이터베이스 개요

file은 성능/보안/편의성에 한계가 있음 → (극복) **database**

<br>

**Database**

- input과 output을 먼저 파악
- input은 데이터의 생성, 수정, 삭제로 나뉘고, output은 읽는 기능 → **CRUD**

![image](https://user-images.githubusercontent.com/71377968/158544083-e5d013f4-c3ec-499d-9040-6e8782b4328a.png)

<br>

**File vs Database**

- file의 경우, 내부 자료의 내용에 따라 데이터를 활용하기 어려울 수 있음
- spreadsheet를 활용해 데이터를 좀 더 확인하기 쉽게 할 수 있음 → Database적인 속성이 있음
- database는 이러한 spreadsheet의 기능을 프로그래밍 언어로 활용할 수 있으며, 자동화하여 사용할 수 있음

<br>

**Database 흐름**

- 기존 database는 주로 관계형 데이터베이스를 주로 사용했었음
- mongoDB(NoSQL)의 등장을 시작으로 다양한 종류의 데이터베이스가 만들어짐
- 각자의 용도에 따라 알맞은 데이터베이스를 사용해야 할 것

<br><br>

### 섹션1 - MySQL 서론

**RDBMS**

관계형 데이터베이스: 데이터를 표의 형태로 정리정돈할 수 있고, 정렬 검색과 같은 작업을 빠르고 편리하고 안전하게 할 수 있음

- MySQL, Oracle, SQL Server, PostgreSQL, DB2, etc.

**MySQL** - 관계형데이터베이스(오픈소스라는 장점 덕에 웹 개발 시에 많이 사용됨)

<br>

**Database의 목적**

- SQL이라는 컴퓨터 언어를 이용해 데이터를 제어할 수 있음
- 웹, 앱, 머신러닝, 인공지능 등에 활용할 수 있음

<br>

**MySQL 설치**

커뮤니티 버전 다운로드 링크: [MySQL :: Download MySQL Community Server](https://dev.mysql.com/downloads/mysql/)

알맞은 운영체제에 맞는 파일을 선택하여 다운로드받을 수 있음

<br>

강의에서는 bitnami에서 WAMP를 활용해 설치하는 방법을 소개한다. 하지만 나는 이미 MySQL을 공식 홈페이지를 통해 설치했기 때문에 강의에서 소개한 간단한 다운로드 방법만 볼 것이다.

bitnami WAMP 다운로드 링크: [WAMP](https://bitnami.com/stack/wamp)

WAMP는 Windows에서 Apache, MySQL, PHP를 한꺼번에 설치할 수 있게 해주는 툴이다. 가장 최신의 버전을 클릭해 파일을 다운로드 받을 수 있다.

mysql은 Windows의 경우, cmd에서 사용할 수 있다. 환경 변수 설정을 통해 어디서든 mysql 명령어를 사용할 수 있게 하면 좀 더 편리하다.

```shell
mysql -u root -p
```

위는 mysql 실행 명령어이다. 해당 명령어를 입력한 후 비밀번호 입력창이 뜨면 초기에 설정한 root 계정의 비밀번호를 입력하면 mysql을 사용할 수 있다.
