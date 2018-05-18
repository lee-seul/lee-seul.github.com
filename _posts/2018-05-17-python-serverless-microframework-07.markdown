---
layout: post
title: "Python Serverless Microframework, Chalice 사용하기, 07"
date: 2018-05-17 20:31:00 +0900
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

이번 장에서는 녹음 파일 업로드 API를 구현한다. 

<br>


### 8. Chalice, 녹음 파일 업로드 API 

<br>

기본적인 틀은 로그인/회원가입 API와 동일하다. 

로직도 간단하다. 

녹음된 파일을 업로드 하는 API이므로 request 바디에 바이너리 값으로 넘어온 파일을 형식에 맞게 처리해주고 

실제 파일은 S3에 저장하고 파일에 대한 정보만 DynamoDB에 저장해주면 된다. 

바로 구현으로 넘어 가보자. 

app.py login API 밑에다 파일 저장 API를 선언한다. 


```python

""" login API 생략""" 

@app.route('/record/{filename}', methods=['PUT'], content_types=['application/octet-stream'])
def record_save(file_name):
    request = app.current_request

    return Response()


```

login API와 유사하지만 app.route안에 들어가는 내용과 login()에는 없던 매개변수가 생겨났다. 

app.route의 매개변수인 file_name은 클라이언트에서 요청을 보낼 떄 url 마지막 부분에 보낸 문자열과 매칭된다. 

예를 들어 /record/test.jpg 라는 url로 요청을 보내면 record_save의 지역 변수 file_name은 test.jpg라는 문자열이 된다. 

뒤에 methods도 GET에서 PUT으로 변경되었는데 PUT 메소드의 경우 Update나 파일 업로드를 위해 쓰이는 메소드이다. 

마지막으로 content_types가 추가되었는데 content_type은 request나 response의 바디에 어떤 형태의 데이터가 들어있는지에 대한 정보를 제공해준다. 

바디의 데이터가 octet-stream은 8비트로 되었다는 뜻이고 여기서는 파일 업로드를 위해 사용한다. 

HTTP 매우 많은 헤더가 있는데 그 중에 Content-Type은 매우 자주 보이고 중요한 헤더이다. 

<br>

이제부터 해야할 작업들을 정리해보면 

1. 요청한 유저가 로그인된 유저인지 확인하고 그에 따른 처리
2. 로그인된 유저라면 파일 저장 처리 

위의 2가지만 해주면 녹음 파일 저장 API도 끝이 난다. 

<br>

**1. 요청한 유저가 로그인된 유저인지 확인하고 그에 따른 처리**

계정별로 업로드 파일을 구분하기 위해 해당 요청이 인증된 유저의 것인지를 확인해야 한다. 

녹음기 앱에서 유저 인증은 Authorization 이라는 HTTP 헤더를 통해서 한다. 

로그인 시 서버가 클라이언트쪽에 auth_key라는 값을 함께 전달하는데 이 값을 요청 헤더에 user_id와 함께 보내면

해당 요청은 인증된 유저의 요청이라고 판단한다. 

인증 부분은 이전 장에서 말했듯이 auth.py에 구현한다. 

auth.py에는 check_authorization과 not_authorization_response 2개의 함수를 정의했고 

정의된 함수는 record_save 내에서 아래와 같이 쓰인다. 


```python


from chalicelib.auth import check_authorization, not_authorization_response 


""" login API 생략""" 


@app.route('/record/{filename}', methods=['PUT'], content_types=['application/octet-stream'])
def record_save(file_name):
    request = app.current_request

    # 인증 부분 추가
    user = check_authorization(request)
    if not user:
        return not_authorization_response(user)

    return Response()


```

check_authorization을 통해 인증된 요청인지 확인하고 인증되지 않은 요청이라면 그에 맞는 response를 반환한다. 

auth.py를 살펴보자. 

<br>


```python
# coding: utf-8


from chalice import Response

from chalicelib.db_handler import DynamoDB


def check_authorization(request):
    if not 'Authorization' in request.headers:
        return False
    
    user_id, auth_key = request.headers['Authorization'].split()

    db = DynamoDB()

    query_item = {
        'id': user_id,
        'auth_key': auth_key
    }

    user = db.get_item('record_user', query_item)
    return user


def not_authorization_response(response_type):
    headers = {'Content-Type': 'application/json'}
    
    body = {'error': 'Not Authorization'}
    status_code = 401
    if response_type is False:
        status_code = 400
        body['error'] = 'Authorization Header is required.'
    
    return Response(
        body=body,
        status_code=status_code,
        headers=headers
    )


```

먼저 check_authorization의 코드를 보면 

request.headers 에 'Authorization'이라는 값이 존재하는지 확인한다. 

만약 값이 없다면 False를 반환하고 함수를 종료한다. 

값이 있다면 Authorization 헤더를 통해 얻은 user_id와 auth_key를 통해

DB에 동일한 user_id와 auth_key를 가진 유저가 존재하는지를 확인한다. 

db.get_item의 경우 db_handler.py에다 3장에서 구현하였다. 

db.get_item은 값이 없을 경우 None을 있다면 해당하는 값을 반환한다. 

<br>

not_authorization_response의 경우에는 check_authorization을 통해 반환 받은 값이 

False나 None일 경우 실행된다. 

False일 경우에는 헤더에 Authorization이 없는 경우 이므로 400 Bad Request를 

None일 경우 유효한 유저가 아니므로 401 UnAuthorized을 반환한다. 

<br>

인증 부분을 완료했으니 요청에 포함된 파일을 S3에 업로드하고 관련 정보를 DB에 저장하기만 하면 된다. 

<br>

**2. 로그인된 유저라면 파일 저장 처리**

다시 app.py로 돌아가서 나머지 코드를 작성해보겠다. 


```python


from chalicelib.auth import check_authorization, not_authorization_response 
from chalicelib.helpers import save_record


""" login API 생략""" 


@app.route('/record/{filename}', methods=['PUT'], content_types=['application/octet-stream'])
def record_save(file_name):
    request = app.current_request

    # 인증 부분 추가
    user = check_authorization(request)
    if not user:
        return not_authorization_response(user)

    return Response()

    body = app.current_request.raw_body
    result = save_record(file_name, user['id'], body)
    data = {'result': 'success'}
    if not result:
        data['result'] = 'fail'

    return Response(
        body=data, 
        status_code=200,
        headers={'Content-Type': 'application/json'}
    )

```

save_record라는 함수를 임포트 하였는데 이건 app.py 나머지 코드를 설명한 다음 설명하도록 하겠다. 

body = app.current_request.raw_body 를 통해 바이너리 값으로 된 데이터를 body에 저장하고 

save_record에 file_name과 user_id와 함께 넘긴다.

save_record에서 DB와 S3에 적절한 데이터들을 저장해준다. 

<br>


```python

import datetime

from chalicelib.db_handler import DynamoDB

import boto3


"""sign_up_or_login 코드 생략 """


def save_record(file_name, user_id, body):

    tmp_file_name = '/tmp/' + file_name
    with open(tmp_file_name, 'wb') as tmp_file:
        tmp_file.write(body)

    s3_client = boto3.client('s3')
    
    now = datetime.datetime.now()

    file_type = ''
    if '.' in file_name:
        file_type = '.' + file_name.split('.')[-1]
    
    BUCKET = 'record-file'
    
    s3_file_name = 'record/{}_{}{}'.format(user_id, now.strftime('%Y-%m-%d_%H-%M-%S'), file_type)
    s3_client.upload_file(tmp_file_name, BUCKET, s3_file_name)
    s3_client.put_object_acl(ACL='public-read', Bucket=BUCKET, Key=s3_file_name)

    url = 'https://s3.ap-northeast-2.amazonaws.com/%s/%s' % (BUCKET, s3_file_name)

    db = DynamoDB()

    s3_key = s3_file_name.split('/')[-1]

    result = db.insert_item(
        'record',
        'record', 
        {
            'id': s3_key,
            'user_id': user_id,
            'file_name': file_name,
            'created_at': now.strftime('%Y-%m-%d')
        }
    )
    if result:
        return True
    return False

```

save_record의 코드는 조금 복잡해보일 수도 있다. 

천천히 설명해보면 바이너리 데이터로 존재하는 파일을 /tmp/file_name 이라는 이름으로 저장한다. 

그런 다음 boto3로 S3와 연결하고 파일을 업로드해준다. 

S3에서 한 버켓에 동일한 이름의 파일이 있을 수 없으므로 파일이름이 겹치지 않도록 파일 명을 변경한다. 

그런 다임 boto3의 upload_file로 파일을 업로드 해주고 put_object_acl을 통해 접근 권한을 설정해준다. 

ACL은 접근 권한에 관한 설정인데 public-read라고 설정하면 모든 사람이 해당 파일을 볼 수 있다. 

**(S3 각 파일에 대한 접근 권한도 계정별로 해주는 것이 맞지만 토이 프로젝트이므로 public-read로 설정한다.)**

<br>

S3에 파일을 올린 다음 접근이 가능하도록 키 값(여기선 s3_file_name)을 DB에 저장해준다. 

아래의 db.insert_item은 앞서 설명한 바가 있으므로 생략한다. 

<br>

그런 다음 save_record의 반환값을 받아 app.py의 record_save에서 적당한 값을 response로 보낸다. 

여기까지가 녹음 파일을 업로드하는 API이다. 

파일 확장자 제한이나 파일에 대한 검사를 하는 코드가 없으므로 사실상 모든 파일을 업로드 할 수 있는 API이다. 

정말 녹음 파일이나 이미지 파일 등 특정 파일로 제한하고 싶다면 추가로 해당 부분을 처리하는 코드를 넣어야 한다.

<br>


이상 파일 업로드 API에 대한 설명을 마무리하겠다. 

이제 API 2개만 더 구현하면 프로젝트가 일단락될 것이다. 

원래 계획은 테스트 코드까지 포함할 예정이었으나 다음 장 진행 상황을 보고 정하도록 하겠다. 

다음 장에서는 나머지 2개의 API를 모두 구현하도록하겠다. 


<br>
<br>

