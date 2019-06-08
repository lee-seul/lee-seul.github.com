---
layout: post
title: "Django migrations"
date: 2019-06-08 19:11:21 +0900
categories: [django]
encoding: UTF-8
---

<br>
<br>

### Django migrations

<br>

Django에서는 DB의 스키마를 migration 파일을 통해 코드 베이스로 관리된다. 

그렇기 때문에 Django에서 model을 만들고 DB에 값을 저장하기 위해서는 migration 파일과 관련된 명령어들을 잘 다룰 수 있어야 한다.

<br>

#### 관련 명령어들

1. makemigrations
Model에 변경점이 있을 때 수행해 준다. makemigrations의 결과물로 migration 파일이 생성된다. 
migration 파일에서 DB에 어떤 부분이 변경되는지 확인할 수 있다. (DB 스키마는 한번 바꾸면 다시 되돌리기 힘든 경우도 있기 때문에 한번 더 확인하는 것이 좋다.
makemigrations는 DB에 바로 적용되는 명령은 아니며, 변경할 부분을 코드로 만들어 놓는 과정이다. 
생성된 migration 파일은 migrate 명령 전이라면 삭제가 가능하다. 그러나 적용 이후에 파일이 삭제되면 makemigrations 명령 실행시 오류가 발생하므로 주의해야 한다.
(이 부분에 대해서는 뒤에서 조금 더 다룬다.)

2. migrate
migrate는 makemigrations의 결과물로 생성된 migrations 파일들에 저장된 DB 스키마 변경점들을 실제로 DB에 적용하기 위한 명령이다. 
명령 실행시 DB의 스키마가 변경되기 때문에 주의해서 적용해야 한다.
명령 실행시 django_migrations 테이블에 적용된 migration 파일의 정보가 기록된다.
--fake 옵션을 통해 실제 스키마는 변경하지 않고, django_migrations 테이블에만 기록할 수 도 있다.(이미 스키마가 적용된 경우)

3. showmigrations
migration 파일이 DB에 적용되었는지 여부를 확인하기 위한 명령이다. 
django_migrations 테이블과 로컬에 있는 migration 파일을 비교한 뒤 적용 여부를 보여준다.

4. sqlmigrate
sqlmigrate의 경우 실제로 어떤 SQL이 실행되는지를 확인할 수 있는 명령이다.


<br>

#### django_migrations 테이블

앞서 말했듯이 django_migrations 테이블을 통해 migration 파일의 적용 여부를 확인한다. 

django_migrations 테이블을 잘 이해하면 migration 파일이 꼬이거나, migration 파일을 정리하고 싶을 때 쉽게 문제를 해결할 수 있다. 

migration 파일은 DB 스키마 변경을 기록하고 관리하기 위한 파일이지, ORM에 영향을 주지는 않는다. 

그래서 migration 파일과는 관계없이 DB에 해당 테이블이 존재할 경우 에러 없이 실행되며, migration 파일이 없더라도 문제가 없다. 

새로운 migration 파일을 적용하고 싶다면, 로컬의 migration 파일과 django_migrations 테이블의 값들을 모두 지우고 makemigrations를 통해 새로운 migration 파일을 생성한 이후 migrate --fake를 실행하면 된다. 



<br>
<br>

