---
layout: post
title: "Python Serverless Microframework, Chalice 사용하기, 06"
date: 2018-05-16 21:22:33 +0900
categories: [python, backend]
encoding: UTF-8
---

<br>
<br>

지금까지 만들어진 서버쪽 

소스 코드는 [https://github.com/lee-seul/recording-server](https://github.com/lee-seul/recording-server) 로 가면 볼 수 있다.

<br>

녹음기 앱을 위한 API는 아래와 같다. 

- 로그인/회원가입
- 녹음 파일 업로드 
- 업로드된 녹음 파일 삭제
- 서버쪽에 저장되어 있는 녹음 파일 리스트 제공


이번 장부터는 필요한 API를 하나씩 구현해 보도록 하겠다. 

첫 번째는 로그인/회원가입 API이다. 

<br>


### 7. Chalice로 로그인/회원가입 API 
<br>


API 구현에 앞서 프로젝트 내에서 하나의 파일에 모든 소스 코드를 넣는 짓을 하지 않기 위해 몇 개의 파일로 코드를 나눌 예정이다. 

app.py 에는 클라이언트에서 보내는 요청을 받는 함수들을 모두 넣을 예정이고 

그 이외에 chalicelib 디렉토리에 auth.py, helpers.py, utils.py로 나눌 예정이다. 

모든 API를 구현한 이후의 chalicelib 디렉토리에 존재하는 파일은 아래와 같다.

```shell
$ ls -la
-rw-r--r--  1 seul  staff     0  5 10 17:40 __init__.py
-rw-r--r--  1 seul  staff   830  5 10 17:40 auth.py
-rw-r--r--  1 seul  staff  4482  5 10 17:57 db_handler.py
-rw-r--r--  1 seul  staff  2363  5 10 17:58 helpers.py
-rw-r--r--  1 seul  staff   821  5 10 17:40 utils.py
```

각각의 파일에 들어갈 코드를 분류해 보자면

- db_handler.py -> DynamoDB를 다루기 위한 코드 4, 5장에서 이미 구현을 마침 
- auth.py -> 인증 관련 코드가 들어갈 파일
- helpers.py -> db_handler.py에 있는 코드들을 활용해 요청 받은 데이터들을 실제로 처리하는 코드들이 들어갈 파일
- utils.py -> 이름 그대로 이런 저런 유틸리티 코드들이 들어갈 파일

이상으로 app.py와 함께 녹음기 앱을 구성할 파일들이다. 


<br>

이제 API를 구현해 보자. 

<br>


소셜 로그인만을 지원할 예정이라 서버쪽에서 할 일은 많지 않다. 

각 소셜 ID를 받아 이전에 가입했는지 확인하고 가입을 했다면 인증에 사용할 키 값을 넘겨 주고, 

가입을 하지 않은 유저라면 가입을 시킨 이후 인증에 사용할 키를 만들어 준 후 넘겨 주기만 하면 된다. 

그러면 이제 app.py을 열어 보자.

보이는 코드는 아래와 같을 것이다. 


```python
from chalice import Chalice

app = Chalice(app_name='record')


@app.route('/')
def index():
    return {'hello': 'world'}


# The view function above will return {"hello": "world"}
# whenever you make an HTTP GET request to '/'.
#
# Here are a few more examples:
#
# @app.route('/hello/{name}')
# def hello_name(name):
#    # '/hello/james' -> {"hello": "james"}
#    return {'hello': name}
#
# @app.route('/users', methods=['POST'])
# def create_user():
#     # This is the JSON body the user sent in their POST request.
#     user_as_json = app.current_request.json_body
#     # We'll echo the json body back to the user in a 'user' key.
#     return {'user': user_as_json}
#
# See the README documentation for more examples.
#
```

app = Chalice(app_name='record') 아래 모든 코드를 지워 주고 

chalice에서 제공하는 Response 모듈을 임포트한다.

그런 다음 login 함수의 기본틀을 잡아보도록 하겠다. 

<br>

```python
# coding: utf-8

from chalice import Chalice, Response

app = Chalice(app_name='record')


@app.route('/login', methods=['GET'])
def login():

    request = app.current_request

    return Response(body='')

```

코드를 살펴보면 flask와 비슷한 구조를 지닌다. 

app.route를 통해 해당 함수를 등록하고 허용할 HTTP method를 정의한다. 

request와 response도 구현했지만 당장 의미는 없다. 

<br>


여기서 추가할 로직은 

1. 요청이 유효한지 체크
2. 유효하다면 가입 여부 확인 
3. 가입 여부에 따라 필요한 처리 
4. 처리가 완료되면 인증에 필요한 키를 클라이언트에 전달 

간단하게 이정도이다. 순서대로 진행해보자. 

<br>
<br>

**1. 요청이 유효한지 체크**

요청이 유효한지 체크하기 위해서는 어떤 요청이 유효한지에 대한 기준부터 정해야한다. 

여기서 구현하는 로그인/회원가입 API는 대부분의 소셜 로그인을 한번에 처리할 예정이다. 

소셜 로그인을 한번이라도 구현해봤다면 대부분의 소셜 서비스가 자체 ID 값을 로그인 시 넘겨준 다는 점을 알 것이다.

보통 이 값을 활용해 유저의 소셜 계정을 식별한다. 

만약 페이스북 소셜 로그인을 통해 가입한 유저라면 페이스북 계정 하나와 우리 서비스 계정 하나가 매칭되어야 한다. 

페이스북의 경우 324234409 와 같은 숫자 값을 넘겨주는데 이걸로 어떤 페이스북 계정인지를 식별할 수 있다. 

그러므로 여기에서도 각 소셜 서비스에서 제공하는 식별자들을 활용하도록 하겠다. 

여기에 더해서 여러개의 소셜 로그인을 모두 지원할 것이므로 각 소셜 서비스 별 프리픽스를 추가할 것이다. 

즉, 페이스북이면 FA, 구글이면 GL, 카카오면 KA 등이며, 프리픽스와 ID값을 혼합하여 FA_324234409 형식의 값을 

유저 테이블의 파티션 키, 필드명 ID 값으로 사용한다. 

<br>

클라이언트 측에서 서버에 필수적으로 넘겨주어야 하는 데이터는 각 소셜 서비스에서 넘겨주는 ID 값과 

어느 소셜 서비스인지 식별할 수 있는 식별자(여기서는 문자열)가 되겠다. 

다시 말해 social_id와 social_type을 넘겨주어야 하므로 이 값이 제대로 넘어왔는 지를 체크하면 

요청이 유효한지를 확인 할 수 있다. 

<br>

말이 길어졌는데 코드로 보면 아래와 같다. 


```python
# coding: utf-8

from chalice import Chalice, Response

app = Chalice(app_name='record')


@app.route('/login', methods=['GET'])
def login():

    request = app.current_request
    
    social_id = request.query_params.get('social_id')
    social_type = request.query_params.get('social_type')
    if not social_id or not social_type:
        data = {'error': 'social_id or social_type is null.'}
        return Response(
            body=data, 
            status_code=400, 
            headers={'Content-Type': 'application/json'}
        )

    return Response(body='')

```

쿼리스트링을 활용해 전달 받기로 미리 약속을 한 상태라 query_params.get을 활용하여 

social_id와 social_type가 요청에 있는지 확인한다. 

query_params도 일종의 딕셔너리라 get 메소드를 활용하여 접근할 경우 default 값이 None이다. 

위 그림처럼 social_id와 social_type이 request에 없다면 None을 리턴한다. 

따라서 if 절에서 social_id나 social_type 중 하나라도 값이 없다면 

400 Bad Request를 클라이언트에 Response로 보낸다. 


<br>
<br>


**2. 유효하다면 가입 여부 확인**

값이 유효하다면 요청을 바탕으로 회원가입/로그인을 처리해주어야 한다. 

요청을 한 유저가 가입을 했는지 하지 않았는지 여부를 판단해야하는데 

social_id와 social_type으로 이루어진 id 값이 DB에 있는지를 확인하면 된다. 

DB와 통신하는 코드이므로 helpers.py에 구현한다. 


```python
# coding: utf-8


from chalicelib.db_handler import DynamoDB
from chalicelib.utils import generate_key, make_user_id


def sign_up_or_login(social_id, social_type):
    db = DynamoDB()

    table_name = 'record_user'

    user_id = make_user_id(social_id, social_type)

    query_item = {
        'name': 'id',
        'value': user_id
    }

    users = db.scan_item(table_name, query_item)
    if users:
        return users[0]['auth_key']

    auth_key = generate_key(64)
    item = {
        'id': user_id,
        'auth_key': auth_key
    }

    result = db.insert_item(table_name, item)
    if result:
        return auth_key
    return False

```

간단한 코드이다. 

social_id와 social_type을 조합해 record_user 테이블에 해당하는 id 값을 가진 로우가 있는지 scan_item을 통해 가입 여부를 확인한다. 


<br>

**3. 가입 여부에 따라 필요한 처리**

가입 여부를 확인했으니 그에 따라 필요한 처리를 해주면 된다.

record_user 테이블에 일치하는 값이 존재한다면 회원가입을 했던 유저이므로 

그대로 auth_key를 반환하여 클라이언트에서 로그인 처리가 되도록 한다. 

반대로 일치하는 값이 없다면 회원가입을 하지 않은 유저로 generate_key를 활용해 auth_key를 만든 다음 DB에 Insert 해준 다음 

auth_key를 반환하면 회원가입 처리와 로그인이 한번에 완료된다. 그 이후는 클라이언트에서 처리해주면 된다. 

<br>

generate_key와 make_user_id에 대한 설명이 빠졌는데 utils.py에 해당 코드를 구현해준다.


```python
# coding: utf-8


import string
import random


def generate_key(n):
    KEY_SOURCE = string.ascii_letters + string.digits
    return ''.join(random.choice(KEY_SOURCE) for _ in range(n))


def make_user_id(social_id, social_type):
    return '{}_{}'.format(social_type, social_id)


```

generate_key는 숫자 n을 인자로 받아 n 길이만큼의 숫자와 문자로 혼합된 문자열을 반환하는 함수이다. 

make_user_id는 social_id와 social_type을 혼합하여 user_id를 만들어주는 함수이다. 

<br>
<br>

**4. 처리가 완료되면 인증에 필요한 키를 클라이언트에 전달**


다시 app.py로 돌아가 로그인/회원가입 API를 마무리하도록 하겠다. 


```python
# coding: utf-8

from chalice import Chalice, Response

from chalicelib.helpers import sign_up_or_login

app = Chalice(app_name='record')


@app.route('/login', methods=['GET'])
def login():
    request = app.current_request
    
    social_id = request.query_params.get('social_id')
    social_type = request.query_params.get('social_type')
    if not social_id or not social_type:
        data = {'error': 'social_id or social_type is null.'}
        return Response(
            body=data, 
            status_code=400, 
            headers={'Content-Type': 'application/json'}
        )

    auth_key = sign_up_or_login(social_id, social_type)  
    if not auth_key:
        data = {'error': 'error'}
        return Response(
            body=data, 
            status_code=400,
            headers={'Content-Type': 'application/json'}
        )

    data = {
        'auth_key': auth_key,
        'user_id': make_user_id(social_id, social_type)
    }

    return Response(
        body=data, 
        status_code=200,
        headers={'Content-Type': 'application/json'}
    )

```

sign_up_or_login을 임포트한 후 social_id, social_type을 활용해 auth_key를 획득한다. 

if not auth_key로 시작하는 부분은 insert_item에서 예기치 못한 에러를 처리해주기 위한 코드이다.

status_code를 400으로 뒀지만 서버 에러이기때문에 500대 에러로 놓는 것이 더 적절하다. 

그 뒤로는 클라이언트에 만들어진 auth_key를 전달하는 코드이다. 

추가로 Response에 headers에 Content-Type을 application/json으로 지정해줬는데 

이 부분은 클라리언트 개발자와 약속한 부분으로 프로젝트에 따라 얼마든지 달라질 수 있다. 

<br>


여기까지가 로그인/회원가입 API에 대한 설명이었고 다음 장에는 녹음 파일 업로드 API에 대해 구현해보겠다. 


<br>
<br>

