---
layout: post
title: Database Study Week2
subtitle: 섹션 2, 3 - 관계DBMS 개념1; 테이블
categories: Database
tags: Database RDBMS MySQL
sidebar: []
---



[강의 링크](https://www.inflearn.com/course/database-2-mysql-강좌/dashboard)

<br>

### 섹션2 - MySQL 기본

**MySQL 구조**

![image](https://user-images.githubusercontent.com/71377968/160275682-96047b3c-94a8-43f2-8700-969ab1f5ee26.png)

- 표(table): 정보가 저장되는 곳
- 데이터베이스(database)/스키마(schema): 연관된 표를 모아놓은 곳(directory와 비슷)
- 데이터베이스 서버(database server): 스키마를 모아놓은 곳

<br>

**데이터베이스 효용**

- 보안성 - 자체적인 보안시스템이 있으므로 좀 더 보안성이 좋음
- 권한 기능 - 사람 별로 읽기/쓰기 등의 기능을 부여할 수 있음
  - 일반적으로 root는 admin임

<br>

MySQL에 로그인하는 것은 데이터베이스 서버에 접속하는 것. 아래 명령어를 입력한 후 패스워드를 입력하면 MySQL 서버에 접속할 수 있음. 이때 p 옵션은 패스워드 입력 옵션이므로 비밀번호가 설정되지 않은 userId로 접속한다면 해당 옵션은 필요없음

```shell
mysql -u[userId] -p
```

<br>

해당 [링크](https://dev.mysql.com/doc/refman/8.0/en/tutorial.html)는 MySQL 8.0 공식 문서임. 해당 문서에서 명령어 등을 확인할 수 있음

<br>

**데이터베이스 만들기**: create 명령어 사용

```sql
CREATE DATABASE [db_name];
```

- create database 명령 입력 후 내가 만들고자 하는 데이터베이스 이름을 작성해주면 됨
- 이때, 항상 명령 끝에는 세미콜론(;)을 붙여야 함

![image](https://user-images.githubusercontent.com/71377968/160275733-0008eaa5-359a-4c33-a22b-582e5bce5f5f.png)

- 사실, CREATE, DATABASE와 같은 SQL 예약어는 대문자로 쓰는 게 좋지만 SQL문은 대소문자를 구분하지 않기 때문에 크게 상관은 없다. 오라클과 같은 곳에서는 해슁 같은 걸 사용하고 기록을 확인하기 때문에 구분해서 쓰는 게 좋다고 한다. 나중에 팀으로 일하게 될 때 정해진 규율대로 쓰도록 하자!
- 쿼리문이 성공하면 두번째줄에 나온 문구가 나옴

<br>

**데이터베이스 리스트 조회**: show 명령어 사용

![image](https://user-images.githubusercontent.com/71377968/160275748-6581c131-75a6-4081-94b5-5fddbc670342.png)

- tutorial database가 잘 생성되어있음을 확인함

<br>

**데이터베이스 사용**: use 명령어 사용

```sql
USE [db_name];
```

![image](https://user-images.githubusercontent.com/71377968/160275765-5c53e150-4ab1-41a1-8b0d-26bae43eda94.png)

- use 명령어를 사용하여 제대로 데이터베이스에 들어가게 되면 데이터베이스가 바뀌었다는 문구가 뜸

<br>

SQL: **S**tructured **Q**uery **L**anguage

- Structured: 표로 정리되어 구조화 된 것
- Query: 데이터베이스에게 무언가를 요청
- Language: 데이터베이스와 사용자의 약속

특징: 쉬움, 중요함( + 표준화된 언어임)

<br>

**표의 구성**

![image](https://user-images.githubusercontent.com/71377968/160275770-7d526921-edc8-44f2-b00f-ba90b4fea04c.png)

- column은 데이터의 type이라고 생각하면 됨
- row는 데이터 하나하나라고 생각하면 됨

<br>

<br>

### 섹션3 - MySQL 테이블의 생성

아래의 명령어를 통해 테이블을 생성할 수 있음.

![image](https://user-images.githubusercontent.com/71377968/160275781-7f588c28-dd80-4273-8bda-2ae31792bb11.png)

```sql
CREATE TABLE [table_name](
	[variable_name] [type] [NULL/NOT NULL] [optional - AUTO_INCREMENT],
	...
	PRIMARY KEY(key_name));
```

- 위처럼 각 column의 이름을 지정하고 type과 NULL 가능성 유무, 그리고 숫자 타입의 데이터의 경우, 자동으로 숫자를 하나씩 증가시켜주는 명령어를 사용하여 테이블을 작성함
- 가장 마지막에는 각 데이터를 대표하는, 중복되어서는 안되는 변수인 primary key의 이름을 써주면 됨
- 나의 경우, 위 명령어를 사용했더니 warning이 떠서 warning이 뭔지 확인해봄. 아래와 같이 그냥 int 타입을 보여줄 때 길이를 정하는 기능이 없어질 수 있다는 것이었음. 딱히 중요한 warning이 아니었으므로 넘어가기로 함!

![image](https://user-images.githubusercontent.com/71377968/160275795-f83882a8-ae78-4fad-9d78-a8722f80467a.png)

- table이 잘 생성되었는지 확인할 때에는 `SHOW TABLES` 명령어를 사용하면 됨
