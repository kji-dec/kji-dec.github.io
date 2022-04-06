---
layout: post
title: Database Study Week4
subtitle: 섹션 5 - 좀더 복잡한 테이블간 관계 배우기
categories: Database
tags: Database RDBMS MySQL
sidebar: []
published: true
---



[강의 링크](https://www.inflearn.com/course/database-2-mysql-강좌/dashboard)

<br>

### 관계형 데이터베이스의 필요성

- 데이터에 중복된 정보가 있는 경우, 해당 데이터베이스에는 개선될 여지가 있는 것임

  → 중복된 정보가 있는 column을 따로 테이블로 빼서 테이블로 만드는 방식으로 개선 가능

- 관계형 데이터베이스를 사용할 경우, 유지보수가 편리하며 별도의 데이터임에도 이름이 같은 경우 등에 대해 중복 데이터가 아님을 알 수 있음

- trade off: 직관적으로 데이터를 볼 수 없음

  - MySQL의 경우, 테이블을 합쳐서 볼 수 있음!

  <br>

  <br>

### Join

```sql
SELECT * FROM [table_name1] LEFT JOIN [table_name2] ON [table_name1].[table_name2_id] = [table_name2_id];]
```

합치려는 테이블의 id를 기준으로 table을 join

<br>

```sql
SELECT [column_names] FROM [table_name1] LEFT JOIN [table_name2] ON [table_name1].[table_name2_id] = [table_name2_id];]
```

표기할 column을 정해서 join

<br>

**실습**

![image](https://user-images.githubusercontent.com/71377968/162006641-cb93bec6-bc8f-4a9b-88ed-ac0c01c535b1.png)

모든 항목이 보이게 join

<br>

![image](https://user-images.githubusercontent.com/71377968/162006754-77181852-0b3d-4066-8b52-37b634ad7134.png)

특정 항목만 보이게 join → error: id값이 중복으로 존재함

<br>

![image](https://user-images.githubusercontent.com/71377968/162006829-6ade1442-dccf-424a-81da-8763dd63372c.png)

앞선 에러 해결 방안: 어떤 table의 id값을 보이게할 것인지 명시해주면 됨

<br>

![img](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/d00b99e2-d231-4e3e-a8af-dabf482e7f43/Untitled.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220406%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220406T150814Z&X-Amz-Expires=86400&X-Amz-Signature=579f2aed492f526efdc4b7cad95b7cbcdaffc974579ad438bcc8ef345afed56b&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22Untitled.png%22&x-id=GetObject)

topic table의 id column명을 topic_id로 변경

<br>

<br>

### 인터넷과 데이터베이스

database client와 database server가 인터넷을 통해 request, response를 주고 받음(정보 관리)

*database client*: request를 server에 보내는 쪽

*database server*: 들어온 request에 대한 response를 보내는 쪽

![image](https://user-images.githubusercontent.com/71377968/162006994-6b845e9c-ee83-45b3-bb06-dcbaea41deeb.png)

<br>

<br>

### MySQL Client

- MySQL monitor: 명령어를 통해서만 제어할 수 있는 클라이언트
- MySQL Workbench: GUI를 이용해 간단히 제어할 수 있는 클라이언트

둘 모두 각각의 장단점이 있으므로 각자에게 맞는 클라이언트를 찾아서 사용하면 됨. 위의 두 클라이언트 외에도 다양한 클라이언트가 있으므로 찾아서 사용하기!

나의 경우, cmd로 주로 사용하기 때문에 cmd로 사용하는 것이 제일 편하다. ~~github를 사용할 때에도 gitbash를 주로 사용하는데 이런 걸 보면 GUI 기반 프로그램을 사용하는 게 좀 낯선 것 같기도 하다. 사실 졸프하면서 fork라는 프로그램을 깔아봤는데 손이 잘 안 간다...~~

