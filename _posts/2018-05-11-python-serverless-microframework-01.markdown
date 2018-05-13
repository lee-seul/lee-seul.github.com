---
layout: post
title: "Python Serverless Microframework, Chalice 사용하기, 01"
date: 2018-05-11 21:27:41 +0900
categories: [python, backend]
encoding: UTF-8
---

<br>
<br>

서버리스 

말은 많이 들었지만 구현은 한번도 해본적이 없었다. 

그러던 중에 동료 안드로이드 개발자분과 조현민 사태를 보면서 

"녹음기 앱 하나 만들면 괜찮지 않을까요?" 라는 대화를 시작으로 새로운 토이 프로젝트를 하게 되었다. 

토이 프로젝트인데 기존에 쓰던 기술만 쓰면 재미가 없으니 

이 참에 서버리스라는 걸로 한번 만들어 보기로 했다. (서버 비용도 아끼고...)

서버리스로 구현할려고 찾다보니 역시나 AWS에서 서버리스를 간단하게 만들 수 있는 

Chalice라는 프레임워크를 진작부터 공개해 놨더라... 

거기다 Python인데다 사용법도 간단해 보여서 일단 Chalice로 구현하고 만들어진 아키텍처를 보면서 공부할 예정이다. 

<br>
지금까지 만들어진 서버쪽 

소스 코드는 [https://github.com/lee-seul/recording-server](https://github.com/lee-seul/recording-server) 로 가면 볼 수 있다.

<br>
<br>
<br>


### 1. 서버리스란? 

<br>

말 그대로 서버가 없다는 뜻이다. 

근데 AWS Lambda나 Azure Functions 등등을 살펴보다 보면 실제론 서버가 존재한다. 

대신 개발을 하면서 서버를 관리할 필요가 없다. 즉, 서버리스라는 말은 서버가 

실제로는 존재하지만 서버에 신경을 쓸 필요가 없다는 말이다. 

서버를 신경쓸 필요가 없다는건 어마어마한 리소스 절약으로 이어진다. 

만약 AWS EC2에 서버를 올린다면 서버 사양부터 시작해서 신경쓸 일들이 

매우 매우 많아지는데 이걸 전부 신경쓸 필요가 없도록 해준다. 

거기다 트래픽에 따라 알아서 확장했다 축소되기도 하고 쓴 만큼만 청구되므로 

합리적인 가격에서 서버를 굴릴 수 있다. 

물론 단점도 많이 있지만 작은 규모의 프로젝트라면 무시할만한 수준이므로 이번엔 그냥 무시하고 진행한다. 

여기서는 AWS에서 제공하는 서버리스용 서비스들을 사용할 예정이다.

Lambda, API Gateway, Dynamodb 등이 사용될 예정이지만 DynamoDB를 제외하고는 

Chalice에서 deploy 명령만으로도 알아서 만들어 지므로 크게 신경쓰지 않아도 된다. 

그래도 궁금하다면 AWS 공식 문서를 찾아보기를 권한다. 

<br>
<br>



### 2. Chalice?


<br>


앞서 소개했듯이 AWS에서 공개한 AWS 서버리스 아키텍처용 Python 프레임워크이다. 

AWS Lambda와 Amazon API Gateway, IAM role 등 필요한 것들을 

자동으로 만들어 주어 정말로 코드에만 신경쓰면 되도록 도와 준다. 

[https://github.com/aws/chalice](https://github.com/aws/chalice)로 가면 전체 소스 코드를 볼 수 있다. 

설명을 자세하게 하고 싶었지만 매우 편리하다는 것 말고는 크게 설명할 것이 없는 것 같다. 

자세한 정보는 [공식 문서 http://chalice.readthedocs.io/en/latest/](http://chalice.readthedocs.io/en/latest/)에서 볼 수 있다. 


<br>
<br>

다음 장에서는 Chalice 설치와 공식 문서에 나오는 Tutorial을 진행할 예정이다. 


<br>
<br>

