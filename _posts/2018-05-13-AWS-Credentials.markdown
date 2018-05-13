---
layout: post
title: "AWS Credentials 설정"
date: 2018-05-13 15:05:59 +0900
categories: [other]
encoding: UTF-8
---

<br>
<br>

### AWS Credentials 설정

<br>

AWS Credentials boto3 등 AWS 서비스를 CLI 환경에서 사용하기 위해서 필요한 인증 수단이다.

IAM을 통해 관련 권한을 설정할 수 있으며, 여러개의 Credentials을 설정할 수도 있다. 

<br>

AWS Credentials은  ~/.aws/config 라는 파일 형태로 추가해주면 된다.

config 파일 안에는 aws_access_key_id와 aws_secret_access_key, region 등을 입력한다. 

**(access_key_id 나 secret_access_key 모두 악용될 소지가 있으므로 타인에게 보여주거나 공개 레포지토리에 올리면 큰일난다.)**

aws_access_key_id와 aws_secret_access_key는 AWS IAM에서 사용자별로 지급된다. 

<br>

AWS console에서 IAM을 찾아서 클릭하면 아래와 같은 화면을 볼 수 있다. 

<br>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/aws_credentials_01.png)

<br>

여기서 사용자를 클릭한다. 

<br>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/aws_credentials_02.png)

<br>

사용자 추가를 클릭

<br>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/aws_credentials_03.png)

<br>

위처럼 사용자 이름과 액세스 유형에서 프로그래밍 방식 액세스를 선택한 후 다음 

<br>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/aws_credentials_04.png)


<br>

권한을 추가해야하는데 그룹을 통해 권한을 할당할 것이므로 그룹 생성을 클릭한다. 

<br>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/aws_credentials_05.png)

<br>

그러면 위와 같은 형태의 창이 나오는데, 여기서 

<br>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/aws_credentials_06.png)

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/aws_credentials_07.png)

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/aws_credentials_08.png)

<br>


IAM, AWS Lambda, Amazon API Gateway 3가지 청책을 그룹에 추가해주고 그룹 이름을 입력한 다음 그룹을 생성한다.

새로 생성한 그룹을 선택한 다음에 다음:검토 버튼을 누른다.

<br>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/aws_credentials_09.png)

<br>

입력한 내용을 확인한 후 사용자 만들기 버튼을 누르면, IAM 사용자가 만들어진다. 

<br>


![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/aws_credentials_10.png)


<br>

그럼 위처럼 액세스 키 ID와 비밀 액세스 키를 얻을 수 있게 된다. 


여기서 얻은 액세스 키 ID와 비밀 액세스 키를 ~/.aws/config 파일에 입력해 준다. 


<br>


```shell
[default]
aws_access_key_id=YOUR_ACCESS_KEY_HERE
aws_secret_access_key=YOUR_SECRET_ACCESS_KEY
region=YOUR_REGION 
```

<br>

config 파일에 값까지 모두 넣어주면 AWS Credential 설정은 끝난다. 

<br>
<br>
