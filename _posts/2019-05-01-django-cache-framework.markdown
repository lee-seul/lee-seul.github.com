---
layout: post
title: "Django에서 Cache 사용하기"
date: 2019-05-02 11:40:04 +0900
categories: [django]
encoding: UTF-8
---

<br>
<br>

### Django Caching

<br>

실 서비스 운영시 대부분의 장애는 DB 부하로부터 시작된다. 

DB 부하를 줄일 수 있는 방법에는 DB에서 실행되는 쿼리의 숫자를 줄이거나, 

쿼리 하나 당 사용하는 DB 서버의 리소스를 줄이는 방법이 있다. 

후자의 경우 최적화가 필요한데, 일반적으로 쿼리 개발보다 최적화가 훨씬 더 많은 시간과 노력이 필요하므로, 

조금 더 간단한 쿼리 숫자를 줄이는 방법에 대해서 설명하려고 한다. 

DB 서버에서 실행되는 쿼리의 숫자를 줄이려면 캐시를 사용하는 방법이 있다. 

Django에서는 간단한 방법으로 캐시를 사용할 수 있다. 

기본적으로는 Memcached를 사용할 수 있지만, 개인적으로 Redis를 더 선호하므로 Redis로 진행한다. 

<br>

#### Redis 설정하기

<br>

설정은 간단하다. django-redis를 설치한 후 settings.py에 CACHE 부분을 추가해주면 된다.

```shell
pip install django-redis
```

<br>

```python
CACHES = {
    'default': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': 'redis://{URL}:6379',
    },
}
```

ElaticCache에 Redis를 띄운 후 설정했다. 

<br>


#### Cache 사용하기

<br>

캐시 사용은 더 간단하다. 

```python

def get_post_count():
        cache_key = 'my_blog_post_count'
        count = cache.get(cache_key, None)
        if not count:
            count = self._get_post_count()
            cache.set(cache_key, count, 60 * 60)
        return count

```

cache.get() 을 통해 캐시에 접근하고 cache.set()을 통해 캐시를 저장한다. 

캐시는 Key-Value로 이루어진 간단한 구조이기 때문에 키를 통해 데이터를 저장하고 불러온다. 

cache.get() 의 경우, cache 키와 default를 지정해 주었고 

cache.set() 의 경우 캐싱할 데이터의 키와 데이터 그리고 유효 기간을 입력해 주었다. 

유효 기간의 단위를 초로 60 * 60으로 설정한 경우 3600초, 즉 1시간 이후에 해당 데이터가 만료된다.

데이터를 캐싱할 때는 유효 기간 설정이 매우 중요한데, 

캐싱이 이루어진 데이터에 변경을 유저에게 언제 노출 시켜줄지에 따라 값이 달라진다.

예를 들어, 오늘 하루 동안 동일한 데이터를 보여줘도 되는 경우 

유효 기간을 오늘이 끝나는 시간으로 설정한다면 하루 동안 동일 요청에 대한 쿼리가 DB에 단 한번만 실행되게 되므로 DB 서버에서 실행되는 쿼리 숫자를 줄일 수 있게 된다.



<br>
<br>

