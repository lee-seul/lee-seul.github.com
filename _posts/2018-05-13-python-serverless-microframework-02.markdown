---
layout: post
title: "Python Serverless Microframework, Chalice 사용하기, 02"
date: 2018-05-13 17:38:02 +0900
categories: [python, backend]
encoding: UTF-8
---

<br>
<br>

지난 장에 이어서 이번 장에서는 Chalice 설치, 설정 및 공식 문서에 나오는 튜토리얼을 진행한다. 

<br>

### 3. Chalice 설정 및 튜토리얼

<br>

여기서 설치 및 설정에 python 가상 환경에 관한 내용은 생략된다. 

python 가상 환경에 관한 내용은 [https://wayhome25.github.io/django/2017/04/29/python-dev-environments/](https://wayhome25.github.io/django/2017/04/29/python-dev-environments/)

**python은 꼭 2.7 아니면 3.6으로 사용**

<br>

가상 환경 설정이 끝났다면 해당 디렉토리로 이동하여 chalice를 설치 한다. 

```shell
$ pip install chalice
```

<br>

설치가 끝났다면 Chalice 프로젝트를 하나 만들어 보자

chalice new-project {프로젝트 이름}이라는 명령어를 사용하면 된다.

```shell
$ chalice new-project record
```

<br>

명령어를 실행하고 나면 {프로젝트 이름}으로 된 디렉토리가 하나 생성된 것을 확인할 수 있다.


```shell
$ cd record
$ ls -la
drwxr-xr-x   .chalice
-rw-r--r--   app.py
-rw-r--r--   requirements.txt
```

<br>

디렉토리에 가보면 위와 같은 구조의 디렉토리가 만들어 진 것을 확인할 수 있다.

여기서 app.py의 내용을 확인 해보면 flask를 사용해본 사람이라면 익숙한 구조의 코드를 볼 수 있다. 

<br>


```shell
$ cat app.py 
from chalice import Chalice

app = Chalice(app_name='record')


@app.route('/')
def index():
    return {'hello': 'world'}

```

<br>

app_name이 record인 애플리케이션에 '/'라는 경로를 가지는 API가 구현되어 있는 것을 볼 수 있다. 

실제 샘플 코드가 동작하는지 보기 위해서 샘플 앱 그래도 배포를 한번 해본다. 

<br>


```shell
$ chalice deploy
```
<br>

chalice deploy를 실행했을 때 

"botocore.exceptions.NoCredentialError: Unable to locate credentials" 

라는 에러를 보게 된다면 AWS credential 설정이 없다는 뜻이므로 credential 세팅을 해줘야 한다.

AWS Credential은 아래 링크 글을 따라하면 된다. 

[https://lee-seul.github.io/other/2018/05/13/AWS-Credentials.html ](https://lee-seul.github.io/other/2018/05/13/AWS-Credentials.html) 


문제가 없거나 AWS Credential을 설정해줬다면 

<br>
<br>

```shell
$ chalice deploy
Creating deployment package.
Updating policy for IAM role: recording-dev
Updating lambda function: recording-dev
Updating rest API
Resources deployed:
  - Lambda ARN: arn:aws:lambda:ap-northeast-2:641123456845:function:record-dev
  - Rest API URL: https://tijmlg2342.execute-api.ap-northeast-2.amazonaws.com/api/
```

<br>

위와 같은 결과를 얻을 수 있다. 

REST API URL을 통해 index라는 이름의 API를 실행해보고면

<br>


```shell
$ curl https://tijmlg2342.execute-api.ap-northeast-2.amazonaws.com/api/
{"hello": "world"}
```

<br>

{"hello":"world"} 라는 바디를 가진 response를 얻을 수 있다. 



<br>
<br>


여기까지 Chalice 공식 문서에 나와있는 튜토리얼을 진행해보았고 다음장에서는 진짜 녹음기 앱을 위한 API들을 구현해 보도록 하겠다. 

<br>
<br>

