---
layout: post
title: "EC2 구성부터 django 서비스 배포까지"
date: 2017-12-20 22:43:11 +0900
categories: [others]
encoding: UTF-8
---


<br/>



### **1. EC2 만들기**

<br/>

#### 1. 대시보드에서 인스턴스 시작 버튼

<br/>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/ec2_01.png)


<br/>


#### 2. Amazon Machine Image(AMI) 선택


<br/>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/ec2_02.png)


<br/>

이번 경우 ubuntu 16.04 LTS 선택

<br/>


#### 3. 인스턴스 유형 선택


<br/>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/ec2_03.png)


<br/>

필요한 사양에 따라 인스턴스 사양 선택 <br/>
테스트 서버 구현이므로 t2.micro 인스턴스 타입 선택한 다음<br/>
<br/>
인스턴스 세부정보 구성으로 이동<br/>

<br/>


#### 4. 인스턴스 세부 정보 구성

<br/>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/ec2_04.png)


<br/>

VPC, IAM 등 설정을 위한 배경 지식이 필요하지만 이번 글에서는 제외 


<br/>



#### 5. 스토리지 추가

<br/>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/ec2_05.png)


<br/>

이 부분에서 필요시 크기 늘려주시면 되고 인스턴스 스토어 / EBS 사용 여부나 프로비저닝된 SSD로 추가 성능 향상이 가능


<br/>



#### 6. 태그 추가(생략)

<br/>


<br/>


#### 7. 보안 그룹 구성

<br/>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/ec2_06.png)

<br/>

기존 보안 그룹에서 설정하셔도 되고, 위 그림처럼 필요한 부분만 개방


<br/>


#### 8. 키페어 생성하거나 기존 키페어 사용

<br/>
<br/>


#### 9. 인스턴스가 생성되는데까지 약간의 시간이 걸립니다

<br/>


아래 메뉴에서 확인 가능 <br/>


![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/ec2_07.png)

<br/>

해당 인스턴스의 초기화가 끝날 때까지 대기 <br/>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/ec2_08.png)

<br/>
<br/>
<br/>

#### 2. Elastic IP 할당

<br/>

전용 테넌시 설정을 하지 않았기때문에 인스턴스 중지/시작시 IP 값이 계속해서 바뀔 수 있고 <br/>
이번 경우 퍼블릭 IP를 할당 받지 않았으므로, Elastic IP를 할당합니다.  <br/>

<br/>

#### 1. Elastic IP에서 새주소 할당

<br/>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/ec2_09.png)


<br/>


#### 2. 할당 받은 IP를 EC2와 연결

<br/>

새로 할당 받은 IP 선택 > 작업 클릭 > 주소 연결 <br/>

<br/>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/ec2_10.png)

<br/>

할당할 인스턴스 선택 후 > 연결

<br/>


#### 3. Elastic IP로 EC2 접속

<br/>

접속 이전에 .pem 키의 권한을 400으로 변경 <br/>
```shell
$ chmod 400 ~.pem
```

<br/>

접속 <br/>
```shell
$ ssh -i “~.pem" ubuntu@ 할당 받은 IP
```



<br/>
<br/>



### 3. 서버 세팅 

<br/>

#### 1. Local 설정

<br/>

접속 후 locale 메세지가 뜰 경우

<br/>

```shell
$ export LANGUAGE=en_US.UTF-8
$ export LC_ALL=en_US.UTF-8
$ sudo locale-gen en_US.UTF-8
$ sudo dpkg-reconfigure locales
```

<br/>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/server_setting_01.png)


<br/>


ko_KR.UTF-8 찾아서 체크(스페이스바) 후 엔터

<br/>
<br/>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/server_setting_02.png)


<br/>

기본 설정은 en_US.UTF-8로 한 후 엔터

<br/>


#### 2. sudo: unable to resolve host ip~ 에러 처리

<br/>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/server_setting_03.png)

<br/>

위 사진처럼 뜬다면, <br/>

/etc/hosts 에 편집해주면 됩니다 <br/>


```shell
$ sudo vim /etc/hosts 
```

<br/>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/server_setting_04.png)


<br/>

127.0.0.1 localhost 아래에 <br/>

( unable to resolve host ip-11-11-11-11 이라고 나왔다면) <br/>

11.11.11.11 ip-11-11-11-11  추가 <br/>


이제 sudo 명령어를 사용해도 에러 메세지가 나오지 않습니다 <br/>

<br/>



#### 3. 서버 패키지 최신화

<br/>

```shell
$ sudo apt-get update && sudo apt-get upgrade 
```

<br/>

 뭔가 물어보는 창이 뜬다면 기존 환경 유지~


<br/>


#### 4. python 개발 환경에 필요한 패지키들 설치

<br/>


```shell
$ sudo apt-get install build-essential libssl-dev libffi-dev python3-dev lib32ncurses5-dev libmysqlclient-dev
```

<br/>



#### 5. git clone 

<br/>

배포하고자 하는 리모트 저장소를 클론합니다. <br/>

```shell
$ git clone <remote_url>
```

<br/>



#### 6. python 패키지 설치를 위한 pip 설치


<br/>

```shell
$ sudo apt-get install python3-pip
```

<br/>



#### 7. 설치한 프로젝트 디렉토리로 이동 후

<br/>

```shell
$ sudo pip3 install -r requirements.txt # 가상환경 세팅 안함
```


pip 업그레이드 

```shell
$ pip3 install —upgrade pip 
```

<br/>

```shell
$ python3 manage.py runserver  # 실행되는지 확인 
```

<br/>
<br/>


### 4. uwsgi & nginx 설정

<br/>


#### 1. uwsgi 설정

<br/>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/uwsgi.png)


<br/>

설정은 위 예시처럼 하시거나 수정하시면 됩니다. <br/>

<br/>

실행 방법은

```shell
$ uwsgi —-ini uwsgi.ini &
```

<br/>

#### 2. nginx 설치

<br/>

nginx 설치 <br/>

```shell
$ sudo apt-get install nginx
```

<br/>

설치 확인

```shell
$ sudo service nginx start
```

<br/>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/nginx_01.png)

<br/>

위 사진처럼 뜬다면 성공

<br/>

#### 3. nginx 설정

<br/>

```shell
$ sudo service nginx stop
```

위 명령어는 안해도 상관없음 


<br/>
/etc/nginx 폴더 밑으로 이동 <br/>

/sites-enabled    sites-available 아래 파일 전부 삭제 <br/>

/conf.d 아래에    설정파일명.conf 파일 만들기 <br/>

/conf.d/파일명.conf  <br/>




```shell
$ sudo vim /etc/nginx/conf.d/파일명.conf 
```


<br/>


설정 예시
<br/>


![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/nginx_02.png)

<br/>

upstream 안 server 127.0.0.1:8081 의 port는 uwsgi 설정에 socket과 일치해야함 <br/>

uwsgi 를 재시작한 다음 <br/>

nginx 시작 <br/>


<br/><br/>