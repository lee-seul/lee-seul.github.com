---
layout: post
title: "Django Silk를 통한 부하 지점 파악하기 "
date: 2019-05-01 11:51:41 +0900
categories: [django]
encoding: UTF-8
---

<br>
<br>

### Django Silk

<br>

Django Silk는 라이브되고 있는 서버의 부하 지점을 알아낼 수 있는 프로파일링 툴이다. 

모든 요청에 대해 요청이 총 걸린 시간과 쿼리에 걸린 시간, 총 쿼리 갯수 등을 프로파일링 해주고, 

최적화 할 수 있는 정보를 제공해준다. 적용도 매우 쉽지만, 

기본적인 동작 방법이 모든 request와 response를 DB에 저장하는 방식이기 때문에 

DB 서버에 부하를 줄 수 있고, 보안 이슈가 생길 수 있다는 문제점이 있지만, 
며
적용이 매우 간단하고 추가 비용이 생기지 않으며, 잘 쓰기만 한다면 해결 가능한 문제들이 때문에 

나쁘지 않은 선택지라고 생각된다.



<br>


#### Django Silk 적용하기 

<br>

적용은 앞서도 말했지만 매우 간단하다. 

django가 설치된 가상환경에 설치해주고 settings.py에 몇 줄 정도의 코드만 추가해주면 된다.

설치
```shell
pip install django-silk
```

<br>

silk는 middleware를 통해 동작하기때문에 SilkyMiddleware를 settings.py의 MIDDLEWARE에 추가해줘야 한다. 

<br> 

```python
MIDDLEWARE = [
    # default middleware
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.locale.LocaleMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.auth.middleware.SessionAuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'django.middleware.security.SecurityMiddleware',
    
    # silk middleware
    'silk.middleware.SilkyMiddleware',

    # GZipMiddleware
    'django.middleware.gzip.GZipMiddleware',

    # custom middleware
    ...
]
```

middleware에 추가해줄 때는 django.middleware.gzip.GZipMiddleware 미들웨어를 제외한 django default middleware 아래에 추가해준다. (모든 custom middleware 위에 위치)

django.middleware.gzip.GZipMiddleware를 사용하고 있다면 GZipMiddleware보다는 이전으로 추가해준다.

<br>

그런 다음 urls.py에 silk에 대한 url을 추가해준다.

```python

# Django 2.0 이상
urlpatterns += [path('silk/', include('silk.urls'))]


# Django 1.11 이하
urlpatterns += [url(r'^silk/', include('silk.urls', namespace='silk'))]

```

그런 다음에 silk에서 사용할 테이블을 migrate를 통해 만들어주고, 디자인을 적용하기 위해 collectstatic을 실행시켜 준다.

```shell
python manage.py migrate
python manage.py collectstatic
```

<br>

기본적인 세팅은 여기까지지만, 실제 live 서버의 경우 이대로 실행하면 몇 가지 문제점이 생기므로 추가적인 세팅이 필요하다. 


<br>


**일부 트래픽에 대해서만 프로파일링 하기**

모든 request, response를 모두 프로파일링하게 되면, request와 response 숫자만큼 

create가 이루어지기 때문에 DB 서버에 부하를 줄 수 있다. 그러므로 일부 트래픽에 대해서만 

적용하는 것을 추천한다. 

설정하는 방법은 매우 간단하다. settings.py에 한줄의 코드만 추가해주면 된다.

```python
SILKY_INTERCEPT_PERCENT = 5
```

이렇게 설정해주면 5%에 해당하는 트래픽만을 silk에서 프로파일링 해준다.

또한 SILKY_MAX_REQUEST_BODY_SIZE, SILKY_MAX_RESPONSE_BODY_SIZE 등을 통해 처리할 request와 response의 body 사이즈를 정해줄 수 있다. (-1을 설정할 경우 제한 없음으로 설정된다)

SILKY_MAX_RECORDED_REQUESTS를 통해 기록할 최대 request 숫자를 지정해 줄 수도 있다.

마지막으로 설정해줘야 하는 것은 접근 권한에 대한 문제인데, 지금처럼 아무런 설정을 안한 경우

url만 알면 모든 사람이 접근할 수 있기 때문에 보안에는 심각한 문제가 생길 수 있다. 

그래서 is_staff가 True인 유저만 적용하려면 SILKY_AUTHENTICATION, SILKY_AUTHORISATION 두 값을 True로 설정해줘야 한다.

```python
SILKY_AUTHENTICATION = True  
SILKY_AUTHORISATION = True
```

추가적인 설정에 관한 내용은 Django Silk Repo에 가면 볼 수 있다.
[Django-Silk Repository Link](https://github.com/jazzband/django-silk)



<br>
<br>

