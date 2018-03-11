---
layout: post
title: "pytest를 이용한 django REST API test 및 travis ci 연동"
date: 2018-03-11 16:26:53 +0900
categories: [django, test]
encoding: UTF-8
---

<br>

테스트의 중요성은 알고 있었으나, 팀 내 백엔드 개발자가 나 혼자라는 핑계로 계속해서 테스트 코드를 짜는 일을 미뤄왔었다.
그러다 디자이너가 당분간 부재하는 상황이 생겼고 이 기회에 최소한의 테스트라도 추가하고자 가장 빠르게 테스트를 도입할 방법을
고민해봤고, Django에서 제공하는 기본 test보다는 pytest를 사용하는 쪽이 더 나을 것이라고 판단하여
pytest를 도입하기로 결정하였다. 

<br>

pytest 사용해본 결과 pytest는 테스트 실행 때마다 DB를 새로만드는 것이 아니라 기존에 settings에 설정된 DB_NAME에 test_라는 
접두어를 붙여 새로운 DB를 생성한 이후 생성한 DB를 계속 사용하는 방식으로 테스트가 진행되어 django test보다 조금 더 빠르게 
실행할 수 있다는 장점과 pytest-xdist pytest-cov 등의 플러그인 등을 통해 필요한 기능을 쉽게 사용할 수 있었다. 

<br>


내가 적용한 Django 프로젝트는 대략 이러한 환경에서 구동 중이다.

**회사 프로젝트**
- python==3.5
- django==1.11
- djangorestframework==3.7.7

**개인 프로젝트**
- python==3.6
- django==2.0
- djangorestframework==3.7.3

<br>

### **pytest**


기존 django 프로젝트 환경에서 pytest를 사용하려면 pytest-django를 설치해주고 조금의 설정만 하면 된다.


**설치**

```shell
$ pip3 install pytest-django
```

<br>

**설정**

설치가 끝났다면 manage.py가 있는 디렉토리에 pytest.ini를 만들고
설정을 추가하면 된다. 


{% highlight python %}

# pytest.ini

[pytest]
DJANGO_SETTINGS_MODULE=for_hg.settings_test
norecursedirs=*/templates/* */templatetags/* */static/* media
addopts=--nomigrations


{% endhighlight %}

<br>

첫 줄의 DJANGO_SETTING_MODULE을 통해 django 프로젝트의 settings파일과 연결시켜준다.

norecursedirs= 에는 테스트할 필요가 없는 파일들을 추가시켜주면 된다.


<br>

**실행**

pytest를 실행시키면 test_라는 접두어가 붙은 모든 파일을 실행시킨다.


```shell
$ python -m pytest
```

<br>

**테스트 커버리지**

pytest에서는 pytest-cov라는 플러그인을 통해 간단하게 테스트 커버리지를 알 수 있다. 


설치는 간단하게 pip를 사용하면 된다.

```shell
$ pip3 install pytest-cov
```

설치 이후에 pytest를 실행할 때 명령어에 --cov를 추가해주기만 하면 된다.

```shell
$ python -m pytest --cov=. 
```

. 는 현재 디렉토리를 말한다.

결과는 아래 그림처럼 출력된다.

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/pytest_cov.png)


<br>

위 결과를 보면 migrations 등이 테스트 커버리지에서 빠져있는데 
.coveragerc 라는 파일을 pytest.ini와 동일한 디렉토리에 만든 다음 아래 처럼 설정했다.

{% highlight python %}

# pytest.ini

[run]
omit = */migrations/*

{% endhighlight %}


<br>

**테스트 병렬 실행**

실행해야할 테스트가 많아지면 실행 시간이 많이 필요해진다. 이럴 때에 고려할 수 있는 것이 병렬 실행이다. pytest에서는 병렬 실행도 pytest-xdist를 통해 간단하게 해결 가능하다.


```shell
$ pip3 install pytest-xdist
```

pytest-xdist 설치 후 실행할 때 


```shell
$ python -m pytest --cov=. -n 3 
```

-n 3 처럼 동시에 실행하고 싶은 숫자를 입력해주면 자동으로 병렬 실행된다. 

<br>

### **Travis CI**

<br>

코드가 변경될 때마다 테스트를 실행해야하는 번거로움을 제거하고자 CI툴을 활용하였다. 
그 중 Travis CI는 공개 저장소에 대해서는 무료로 사용이 가능하며, 설정이 매우 간단하다. 

Travic CI (org)에 들어간 후 github 등 계정을 연결하고 해당 저장소에 권한을 부여해준 후 project 루트 디렉토리에 .travis.yml 파일을 추가해주기만 하면된다. 


{% highlight python %}

# .travis.yml

language: python
python:
    - "3.5"
    - "3.6"

dist: trusty
sudo: rquired

env:
    - DJANGO_VERSION==2.0


install:
    - pip3 install -q Django=$DJANGO_VERSION
    - pip3 install -q -r requirements.txt

script:
    - cd for_hg
    - touch problem/secrets.py
    - echo "AWS_ACCESS_KEY_ID=''" >> problem/secrets.py
    - echo "AWS_SECRET_ACCESS_KEY=''" >> problem/secrets.py
    - python3 manage.py migrate
    - python3 -m pytest -n 3 --cov-config .coveragerc --cov=.

{% endhighlight %}


위처럼 설정하면 푸시할 때마다 script에 해당하는 부분이 실행되고 테스트 성공 여부를 알려준다.


<br>
