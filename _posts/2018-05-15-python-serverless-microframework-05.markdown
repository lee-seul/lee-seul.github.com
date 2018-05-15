---
layout: post
title: "Python Serverless Microframework, Chalice 사용하기, 05"
date: 2018-05-15 19:58:47 +0900
categories: [python, backend]
encoding: UTF-8
---

<br>
<br>

지금까지 만들어진 서버쪽 

소스 코드는 [https://github.com/lee-seul/recording-server](https://github.com/lee-seul/recording-server) 로 가면 볼 수 있다.

<br>

3 장에서 설계한 테이블의 구조는 아래와 같다. 


* 회원정보 테이블
    * id (partition key), 유저를 식별하기 위한 값 
    * auth_key (sort key), 앱과의 통신 도중 인증을 위해 필요한 값 

<br>

* 녹음 파일 테이블
    * id(partition key), 파일을 식별하기 위한 값
    * user_id(sort key), 어떤 유저가 업로드한 파일인지를 확인하기 위한 값


<br>

이번 장에서는 설계한 테이블 구조를 이용해 DynamoDB 테이블을 생성하는 코드를 작성하고 실제로 동작하는지 확인한다. 

<br>


### 6. DynamoDB 테이블 생성 

<br>

```shell
$ ls -la
drwxr-xr-x  4 seul  staff  128  5 11 18:06 .chalice
-rw-r--r--  1 seul  staff  731  5 11 18:06 app.py
drwxr-xr-x  3 seul  staff   96  5 14 20:43 chalicelib
-rw-r--r--  1 seul  staff    0  5 11 18:06 requirements.txt
```

현재 프로젝트 디렉토리의 상태는 위와 같다. 

chalice deploy를 통해 AWS Lambda에 배포될 수 있는 파일은 app.py와 파이썬 패키지 chalicelib 밖에 없다. 

현재 위치에서(app.py와 같은 위치)에서 .py 파일을 추가하더라도 chalice deploy 명령 실행시 배포되지 않고 로컬에만 존재하게 된다. 

지금부터 구현할 파일은 DynamoDB에 테이블을 생성하는 일회성 코드로 굳이 배포될 필요가 없으므로 

현재 위치에서 create_table.py라는 이름으로 새로운 파일을 만들겠다. 


<br> 

```shell
$ vim create_table.py
```

<br>
먼저 바로 이전 글에서 만들었던 db_handler.py에 DynamoDB를 임포트한 다음 

user 테이블과 record 테이블을 만들기 위한 함수를 선언해준다. 



```python
# coding: utf-8


from chalice.db_handler import DynamoDB


def create_user_table():
    pass


def create_record_table():
    pass


if __name__ == '__main__':
    create_user_table()
    create_record_table()

```

최하단에 if 절은 create_table.py가 다른 곳에 임포트되지 않고 자체적으로 실행될 때에만 

if 절 안의 코드들이 실행되도록 하는 코드들이다. 

<br>



```python

""" 윗 부분 생략 """


def create_user_table():
    db = DynamoDB()

    table_name = 'record_user'
    
    hash_dict = {
        'AttributeName': 'id',
        'AttributeType': 'S'
    }

    range_dict = {
        'AttributeName': 'auth_key',
        'AttributeType': 'S'
    }

    table = db.create_table(table_name, hash_dict, attrs=[], range_dict=range_dict)

    print(table)


""" 아랫 부분 생략 """

```

table_name과 hash_dict, range_dict 등 테이블을 정의할 값들을 선언해준다. 

hash_dict와 range_dict에서 쓰인 AttributeType은 각 필드의 데이터 타입을 정의해주는데 

S(문자열), N(숫자), B(바이너리) 3개의 값이 올 수 있는데 위의 예시처럼 하나만 올수 도 있고 

콤마로 구분하여 'S,N' 등으로 한번에 여러 값이 올 수 도 있다. 

<br>


```python

""" 윗 부분 생략 """


def create_record_table():
    db = DynamoDB()

    table_name = 'record_record'
    
    hash_dict = {
        'AttributeName': 'id',
        'AttributeType': 'S'
    }

    range_dict = {
        'AttributeName': 'user_id',
        'AttributeType': 'S'
    }

    table = db.create_table(table_name, hash_dict, attrs=[], range_dict=range_dict)

    print(table)


""" 아랫 부분 생략 """

```

record 테이블도 위에서 설명한 user 테이블과 크게 차이가 없기 때문에 설명은 생략한다. 

<br>


완성된 파일을 실행해보면 아래와 같은 결과가 나온다. 

AWS console에서 DynamoDB에 가보면 2개의 테이블이 생성된 것을 볼 수 있다.


```shell
$ python create_table.py
record_user
record_record
```

<br>


만약 실행 부분에서 오류가 난다면 IAM에서 role에 정책을 연결해주었는지 확인하기 바란다. 

해당 부분은 아래 글에서 확인이 가능하다. 

[https://lee-seul.github.io/python/backend/2018/05/13/python-serverless-microframework-03.html](https://lee-seul.github.io/python/backend/2018/05/13/python-serverless-microframework-03.html)



<br>
<br>

이제 DB도 만들었으니 다음 장부터는 Chalice를 써서 API들을 하나씩 구현해 보도록 하겠다. 

<br>
<br>

