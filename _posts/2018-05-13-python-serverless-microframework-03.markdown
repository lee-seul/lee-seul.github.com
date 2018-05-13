---
layout: post
title: "Python Serverless Microframework, Chalice 사용하기, 03"
date: 2018-05-13 17:38:02 +0900
categories: [python, backend]
encoding: UTF-8
---

<br>
<br>

지금까지 만들어진 서버쪽 

소스 코드는 [https://github.com/lee-seul/recording-server](https://github.com/lee-seul/recording-server) 로 가면 볼 수 있다.

<br>

이번 장부터는 녹음기 앱을 설계부터 구현, 테스트 코드 작성까지의 내용을 하나씩 다뤄보고자 한다. 

<br>


설계에 앞서 S3와 DynamoDB를 추가로 사용할 예정이므로 관련 권한을 설정해주고 boto3를 설치해준다. 

boto3는 python으로 AWS를 간편하게 사용할 수 있도록 도와주는 SDK이다. 

boto3를 활용하면 코드 상에서 AWS의 리소스들을 매우 쉽게 사용할 수 있다 

boto3는 pip install을 통해 매우 간단하게 설치할 수 있다. 

<br>

```shell
$ pip install boto3
```

<br>

boto3의 설치가 끝났다면 S3와 DynamoDB에 대한 권한을 chalice deploy를 통해 만들어진 IAM role에 추가해준다. 

AWS IAM 대시보드에 들어가보면 왼쪽 메뉴에 역할이라는 탭이 있다 

IAM role에 대한 탭인데 클릭해서 이동한다. 


<br>


![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/chalice_01.png)

<br>

역할탭으로 가보면 

<br>


![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/chalice_02.png)

<br>

지난 번 chalice deploy로 인해 role이 이미 하나 생성되 있는 것을 볼 수 있다. 

해당 롤을 클릭하여 상세 내용으로 간 다음 

<br>


![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/chalice_03.png)


<br>

정책 연결을 통해 AmazonS3FullAccess와 AmazonDynamoDBFullAccess 2개를 추가해준다. 

그러면 우리의 chalice 프로젝트에서 boto3를 통해 S3와 DynamoDB를 사용할 수 있게 된다.

<br>
<br>



### 4. 녹음기 앱 설계

<br> 

이제 대부분의 설정이 끝났으므로 앱을 설계해보자

<br>

간단한 녹음기 앱이기때문에 ‘녹음’ 기능만을 지원할 예정이지만 서버 API가 필요하도록 기능 몇가지를 추가했다.

- 소셜 로그인
- 녹음 파일의 백업 및 다운로드

2가지 기능을 추가하여 서버에서 해야할 일을 만들어 줄 수 있었다. 

<br>

위의 기능을 지원해주기 위해 서버에서 만들어야 할 API는 

- 로그인/회원가입
- 녹음 파일 업로드 
- 업로드된 녹음 파일 삭제
- 서버쪽에 저장되어 있는 녹음 파일 리스트 제공

이렇게 4가지이다. 

<br>

4개의 API를 지원하기 위해서는 데이터베이스가 필요하다. 

데이터베이스는 앞서 말했듯이 DynamoDB를 사용한다. 

DynamoDB는 key-value data stores 방식의 NoSQL 데이터베이스이다. 

<br>

참고로 NoSQL 데이터베이스에는 

* Key-value data stores
* Document stores
* Wide-Column stores
* Graph stores
4가지 형태가 있다. 

<br>

DynamoDB에서는 스키마를 강제하지 않아 미리 형식을 정의할 필요가 없다.

또한 기본키(파티션키) 이외에 정렬키라는 것을 추가로 설정할 수 있다. 

<br>

먼저 로그인/회원가입을 구현하기 위한 회원 정보 테이블과 업로드된 파일을 관리할 녹음 파일 테이블이 필요하다. 

회원 정보 테이블에는 각 인스턴스를 식별할 파티션키와  앱에서 서버에 요청을 할 때 로그인이 되었다는 것을 

알려주기 위해 Authorization Header에 포함시킬 auth_key가 저장되어야 한다. 


녹음 파일 테이블에는 녹음 파일을 식별할 파티션 키와 어떤 유저의 파일인지를 확인하기 위해 user의 파티션 키를 저장한다. 

<br>
<br>

각 테이블과 필요한 필드들을 정리해보면 

* 회원정보 테이블
    * id (partition key), 유저를 식별하기 위한 값 
    * auth_key (sort key), 앱과의 통신 도중 인증을 위해 필요한 값 

<br>

* 녹음 파일 테이블
    * id(partition key), 파일을 식별하기 위한 값
    * user_id(sort key), 어떤 유저가 업로드한 파일인지를 확인하기 위한 값


<br>

녹음 파일 저장 시에 파일명과 저장한 날짜를 추가로 저장할 예정할 것 이지만 

스키마를 미리 정의할 필요가 없으므로 필수적인 값들만 선언해준다. 

<br>


이번 장에서 DynamoDB에 실제 테이블까지 만들어 볼 생각이었으나 내용이 너무 길어지는 것 같아 실제 테이블을 만드는 작업은 다음 장에서 진행하도록 한다. 


<br>
<br>
