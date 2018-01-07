---
layout: post
title: "Django에서 MySQL 4바이트 이모티콘 저장하기"
date: 2018-01-07 19:13:31 +0900
categories: [django, backend]
encoding: UTF-8
---

<br>


### **이모티콘 종류에 따라 MySQL에 제대로 Insert되지 않는 문제**

<br/>

정말 소수의 이모티콘을 제외하고는 Android/iOS 등에서 사용하는 이모티콘은 4바이트이고 
utf8로 인코딩이 설정된 MySQL에서 받을 수 있는 문자열은 3바이트까지만 지원하기때문에 

Incorrect string value 라는 에러를 만나게 된다.


즉, 일반적으로 사용하는 이모티콘을 모두 MySQL에 저장하기위해서는 인코딩을 변경해주어야한다.

인코딩은 문자 하나당 4바이트를 지원하는 utf8mb4로 변경해주고 collation도 utf8mb4_unicode_ci로 변경해준다. 

디폴트 세팅만 변경해야하는 것이 아니라 이모티콘이 저장되는 테이블의 인코딩과 칼럼까지 변경해주어야 입력이 된다.



<br>


그러나 테스트시 Sentry에서 계속해서 Incorrect string value가 나는 것을 확인할 수있었다.


그래서 일단 DB에 직접 저장이되는지 시도했더니 DB에는 정상적으로 Insert가 된다는 것을 알게 되었고 Django ORM에서 이모티콘 처리를 못하는 것이 아닐까라는 생각이 들어 해당 내용을 찾아보았더니

settings에 DATABASE에 옵션으로 인코딩을 줄 수 있는 것을 발견


<br>

{% highlight python %}

# settings.py


DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': '<DB_name>',
	'USER': '<user_name>',
	'PASSWORD': '<password>',
	'HOST': '<DB_host>',
    	'PORT': '3306',
        'OPTIONS': {
            'init_command': "SET sql_mode='STRICT_TRANS_TABLES'",
            'charset': 'utf8mb4',
            'use_unicode': True,
        },
    }
}


{% endhighlight %}

<br>

위 처럼 설정을 바꾸고 실행했더니 정상적으로 처리가 되는 것을 확인 할 수 있었다.