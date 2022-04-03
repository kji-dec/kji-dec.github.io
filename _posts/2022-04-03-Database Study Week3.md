---
layout: post
title: Database Study Week3
subtitle: 섹션 4 - 관계DBMS 개념2; CRUD 쿼리
categories: Database
tags: Database RDBMS MySQL
sidebar: []
---



[강의 링크](https://www.inflearn.com/course/database-2-mysql-강좌/dashboard)



### 섹션 4 - CRUD

✍🏼 CRUD - **C**reate **R**ead **U**pdate **D**elete

<br>

우선, 데이터를 추가하기에 앞서서 지난시간에 추가한 table에 어떤 데이터를 넣어야할지 확인할 것이다.

```sql
DESC [table_name];
```

DESC 명령어를 사용하면 field와 type 등을 알아낼 수 있다.

![image](https://user-images.githubusercontent.com/71377968/161420343-e2ce6f66-6ebb-40a5-bd5e-0122021c7b9a.png)

<br><br>

**Create**: 데이터를 추가하는 것 - **INSERT** 사용

```sql
INSERT INTO topic (title,description,created,author,profile) 
VALUES("MYSQL","Mysql is ...",NOW(), "Jingnii","developer");
#NOW()는 현재 시간을 알려주는 함수
```

INSERT INTO 명령어 후에 테이블명, (column 이름 나열), VALUES 명령어 후에 각 (column의 데이터값)을 입력해주면 된다. 이제 작성한 데이터를 확인하려면 Read 해야한다. 참고로, read를 진행하기 전에 데이터를 좀 더 넣어보았다.

<br><br>

**Read**: 데이터를 읽는 것 - **SELECT** 사용

```sql
#1. topic table에서 모든 데이터를 확인하는 SQL문
SELECT * FROM topic;

#2. id,title,created,profile column만 조회하는 SQL문
SELECT id,title,created,profile FROM topic;

#3. author column에서 "Jingnii"인 것만 조회하는 SQL문
SELECT id,title,created,profile FROM topic 
WHERE author="Jingnii";

#4. 정렬하여 데이터를 조회하는 SQL문
SELECT id,title,created,profile FROM topic 
ORDER BY id DESC;

#5. 한정된 수의 데이터만 조회하는 SQL문
SELECT id,title,created,profile FROM topic
WHERE title="MYSQL"
ORDER BY id DESC 
LIMIT 2;
```

SELECT와 FROM 사이에 조회하길 원하는 column의 이름을 적어주면 된다. 이때 첫번째로 작성한 쿼리에서 *의 의미가 바로 ‘모든 것’이라는 의미이다.

![image](https://user-images.githubusercontent.com/71377968/161420347-683db38f-6ed6-4db5-987e-af6a1faba7f1.png)

<br>또, 특정 column만 조회하는 명령어를 사용했을 때에는 지정된 column 외의 column에 관한 데이터는 조회되지 않음을 확인할 수 있다.

![image](https://user-images.githubusercontent.com/71377968/161420384-1b8af5f1-d4df-4d9b-89be-a6d2214b28bd.png)

<br>SELECT문에는 조건도 추가할 수 있는데, 3번 SQL문에서도 알 수 있듯 FROM [*table_name*] 이후에 WHERE 명령어를 사용하면 된다.

![image](https://user-images.githubusercontent.com/71377968/161420419-80bc3de4-babb-4fea-b9fa-1d2f581cf87f.png)

<br>네번째 SQL문은 정렬 기능의 SQL문이다. ORDER BY 명령어를 사용하여 정렬을 하는데, 그 이후에 들어온 id라는 아이가 정렬의 기준이 되고, DESC라는 명령어는 내림차순이라는 의미를 가지고 있다. 즉, id를 기준으로 내림차순으로 데이터를 정렬하는 것이다. 참고로 오름차순은 ASEC이다

![image](https://user-images.githubusercontent.com/71377968/161420427-37e56532-5693-48a5-a175-73bd71b9d04d.png)

<br>또, 한정된 개수의 데이터만 가져올 수도 있는데 LIMIT을 사용하는 것이다. LIMIT 명령어 뒤에 바로 그 수를 입력해주면 된다.

![image](https://user-images.githubusercontent.com/71377968/161420447-23524036-1ec1-4852-9752-1fccc619b631.png)

<br><br>**Update:** 데이터를 수정하는 것 - **UPDATE...SET** 사용

```sql
UPDATE topic 
SET description="Oracle is ..", title="Oracle" 
WHERE id=1;
```

UPDATE 명령어 후에 테이블 명을 적어준 후, SET 이후에 수정할 데이터를 입력해준다. 그리고 where문을 사용하여 어떤 데이터를 바꿀 건지를 적어줘야 한다.

![image](https://user-images.githubusercontent.com/71377968/161420458-214c3938-e4fb-4fa6-b0d1-a0612e713942.png)

update구문 후에 데이터를 확인해보면, 1번 id의 데이터가 잘 수정되었음을 알 수 있다.

<br><br>**Delete**: 데이터를 삭제하는 것 - **DELETE** 사용

```sql
DELETE FROM topic 
WHERE id=5;
```

DELETE FROM 후 테이블 명을 적어준 후, WHERE 조건을 통해 어떤 데이터를 삭제할 것인지 적어줘야 한다. 그렇지 않으면 모든 데이터가 삭제될 수 있으니 주의하자!

![image](https://user-images.githubusercontent.com/71377968/161420468-6da6e0c4-a70a-45b3-ae8a-8a58039588e6.png)

delete 구문 후에 데이터를 조회했을 때, id가 5인 데이터가 정상적으로 지워졌음을 확인할 수 있다.
