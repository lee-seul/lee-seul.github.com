---
layout: post
title: "Python Serverless Microframework, Chalice 사용하기, 08"
date: 2018-05-18 20:3:00 +0900
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

이번 장에서는 업로드된 녹음 파일 삭제 API와 서버쪽에 저장되어 있는 녹음 파일 리스트 API를 구현한다. 

드디어 Chalice 녹음기 앱 포스팅 마지막 장이다. 

어서 마무리를 지어보자.   

<br>


### 9. Chalice, 업로드된 녹음 파일 삭제 API

<br>

삭제 API는 말그대로 유저가 8장에서 구현한 업로드 API를 통해 업로드한 녹음 파일을 삭제하는 API이다. 

업로드 기능이 회원에게만 지원되므로 삭제 기능 또한 회원에게만 지원된다. 

삭제는 S3에서 실제 파일을 삭제하는 것과 DB에 저장된 녹음 파일에 대한 정보를 지우는 2개의 작업을 처리해주어야 한다. 

app.py 기존 코드 아래에 작성한다. 


```python

from chalicelib.helpers import delete_recording

""" 생략 """


@app.route('/record/{record_id}', methods=['DELETE'])
def record_delete(record_id):
    request = app.current_request
    user = check_authorization(request)
    if not user:
        return not_authorization_response(user)

    result = delete_recording(record_id, user['id'])

    if not result:
        data = {'result': 'fail'}
        return Response(
            body=data,
            status_code=400,
            headers={'Content-Type': 'application/json'}
        )    
    return Response(
        body={'result': 'success'},
        status_code=200,
        headers={'Content-Type': 'application/json'}
    )    


```

url을 통해 record_id를 받아 해당 record 파일을 삭제한다. 

삭제하는 코드는 delete_recording이라는 함수로 chalicelib 디렉토리에 helpers.py에 구현했다. 

delete_recording이 성공/실패 여부에 따라 status code를 처리해준다. 

check_authorization, not_authorization_response 등의 나머지 코드는 업로드 API 때 설명했으므로 생략하겠다. 

<br>

helpers.py에서 delete_recording을 살펴보면 


```python

import boto3

from chalicelib.db_handler import DynamoDB


def delete_recording(record_id:int, user_id:int):

    db = DynamoDB()

    table_name = 'record_record'

    item = {
        'id': record_id,
        'user_id': user_id
    }

    result = db.delete_item(table_name, item)
    if result:
        BUCKET = 'record-file'
        s3 = boto3.resource('s3')

        s3_key = 'record/{}'.format(record_id)
        s3.Object(BUCKET, key=s3_key).delete()
        return True
    return False 

```

record_id와 user_id값을 이용해 DB에서 해당 값을 삭제한 다음 DB에서 삭제가 정상적으로 이루어지면

S3에서까지 삭제하여 실제로 파일을 사라지도록 한다. 

만약 고객 요청 등을 고려해 몇일 간의 유예 기간을 주고 싶다면 DB에 expired_date 등의 값을 추가하고 

유저가 보는 리스트에서는 제공해주지 않았다가 expired_date에 해당하는 날에 실제로 삭제를 시키는 방법이 있다. 

S3 접근, DB 접근 등 앞서 모두 설명한 내용이기 때문에 추가적인 설명은 생략하고 넘어가도록 하겠다. 

마지막 API인 녹음 파일 리스트 API이다. 

<br>

### 10. Chalice, 녹음 파일 리스트 API 

<br>

각 API를 한번에 클라이언트 개발자에게 전달할 것이 아니라면 삭제 이전에 리스트 API를 먼저 만드는 것이 맞다.

왜냐면 삭제를 위해서는 record_id가 필요한데 리스트 API를 통해 리스트의 목록을 받기 전에는 파일이 DB에 어떻게 저장되어 있는지 

클라이언트쪽에서는 알 수가 없다. 만약 API를 순차적으로 전달할 예정이라면 클라이언트에서 구현할 수 있는 순서를 고려해주는 것도 좋은 방법이다.

<br>

녹음 파일 리스트 API는 사용자가 업로드한 녹음 파일의 목록을 뿌려주기 위한 API이다. 

app.py 제일 아래 record_list라는 이름으로 구현한다. 


```python

from chalicelib.helpers import get_records

""" 생략 """


@app.route('/record/list', methods=['GET'])
def record_list():
    request = app.current_request
    user = check_authorization(request)
    if not user:
        return not_authorization_response(user)

    records = get_records(user)
    data = {
        'records': records
    }

    return Response(
        body=data,
        status_code=200,
        headers={'Content-Type': 'application/json'}
    )


```

짧게 설명해보면, check_authorization을 통해 가입 여부를 체크하고 가입되어 있다면 

get_record를 통해 유저가 업로드한 파일 리스트를 가져와서 response로 보낸다. 

get_records 역시 데이터를 처리하는 부분이므로 helpers에 구현한다. 

<br>


```python

from chalicelib.db_handler import DynamoDB


def get_records(user):
    db = DynamoDB()
    return db.scan_item(
        'record_record',
        {
            'name': 'user_id',
            'value': user['id']
        }
    )

```

get_records는 매우 간단하다. 

user_id를 통해 DynamoDB를 scan하여 요청을 보낸 유저가 저장한 파일의 리스트를 얻어 온다. 

scan_item의 경우 리스트가 반환된다. 

반환된 값을 딕셔너리 형태로 보내면 완성이다. 

<br>

여기까지 Chalice로 녹음기 애플리케이션 만들기가 마무리 되었다. 

처음 시작할 때는 2-3편 정도로 마무리할 생각이었는데 진행을 하다보니 이것 저것 설명할 것이 많아 8편이나 포스팅하게 되었다. 

아직 많이 미숙한 개발자이다 보니 모르는 부분도 많고 틀린 설명도 많은데 지적할 부분이나 궁금한 부분이 있으면 

정말 편하게 blacksangi@gmail.com으로 문의하면 된다. 최대한 빠르게 답변해드리겠다. 

누가 읽을지는 모르겠지만 읽어준 분이 있다면 감사하다는 말을 전하고 싶다. 


<br>

끝!


<br>
<br>

