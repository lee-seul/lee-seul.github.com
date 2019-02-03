---
layout: post
title: "Django model별 MySQL storage engine 적용하기"
date: 2018-11-28 20:14:53 +0900
categories: [django]
encoding: UTF-8
---

<br>
<br>

### Django model별 MySQL storage engine 적용하기

<br>


MySQL에서는 여러가지 storage engine이 존재하는데 그 중 InnoDB와 MyISAM이 가장 유명하다. 

그에 대한 설명은 이전 포스팅에서 확인 가능하므로, 생략
[https://lee-seul.github.io/backend/2017/12/19/MySQL-storage-engine.html](https://lee-seul.github.io/backend/2017/12/19/MySQL-storage-engine.html)

<br>

MySQL, Django 스택의 개발 환경이라면 대부분이 InnoDB를 기본 storage engine으로 쓰고 있을 것이다. 

그러나 다양한 개발 요구 사항과 성능 등의 이슈로 테이블 별로 다른 storage engine을 적용해야 하는 상황이 있을 수 있다.

특히, 로그의 경우 무결성 체크 등의 기능이 필요 없고, 로그로 인한 성능 이슈가 생기지 않도록 처리해야하기 때문에 

InnoDB보다는 MyISAM이 더 나은 선택이 될 수 있다. (NoSQL이 대안이 될 수 있지만, 개발 비용이 들거나 Dynamo처럼 서버 비용 이슈가 있다)

<br>

그런데 Django 문서나 코드를 봐도 Model 별로 storage engine을 세팅할 수 있는 방법은 없고 

문서상에도 나와있듯이 조금 귀찮은 2가지 방법으로 적용할 수는 있다. 

2가지 방법은 SQL을 이용하는 방법과 Django 세팅에서 해당 테이블을 만들기 전에 기본 storage engine을 바꾸는 방법이 있다.

<br>

SQL을 이용하는 방법은 매우 간단하다. 

먼저 테이블을 만든 다음, django shell이나 MySQL에 직접 접근하여 쿼리를 날리면 된다. 쿼리는 아래와 같다. 

```sql
ALTER TABLE <tablename> ENGINE=MyISAM;
```

<br>

두번째 방법은 조금 귀찮은데 migrate 명령을 실행하기 전에 django DB 세팅에 아래와 같은 코드를 추가한 다음 

migrate 명령을 실행하면 된다. 

```python
'OPTIONS': {
   'init_command': 'SET default_storage_engine=MyISAM',
}
```
migrate 종료 후에 다시  InnoDB로 기본 세팅을 바꾸는 걸 잊으면 안된다. 




<br>
<br>