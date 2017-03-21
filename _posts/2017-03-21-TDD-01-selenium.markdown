---
layout: post
title: "TDD in python, 01 "
date: 2017-03-20 23:48:22 +0900
categories: [test, django]
encoding: UTF-8
---

(파이썬을 이용한) 클린 코드를 위한 테스트 주도 개발 책 내용을 공부하면서 정리한 글

작업 환경
- macOS Sierra, 10.12.3
- python, 3.6.0
- django, 1.9.7
- selenium, 3.3.1
- firefox, 52.0.1

<br/>


### 1. 기능 테스트를 위한 Selenium

새로 디렉토리를 하나 만들고, 가상환경을 세팅한 이후 selenium과
django를 설치한다. 

```python
$ pip install django==1.9.7
```

```python
$ pip install selenium
```

설치한 이후 아래의 python코드를 입력하고 실행해본다. 

{% highlight python %}

# functional_test.py

from selenium import webdriver

browser = webdriver.Firefox()
browser.get('http://localhost:8000')

assert 'Django' in browser.title

{% endhighlight %}

<br/>
<br/>

```python
$ python functional_test.py # 실행하면 오류
```

해당 파일을 샐힝하면 FileNotFoundError: [Errno 2] No such file or directory: 'geckodriver' 라는 오류가 뜬다

이걸 해결하기 위해서는 
[mozilla/geckodriver](https://github.com/mozilla/geckodriver/releases)
에서 os에 맞는 geckodriver를 다운 받아 /usr/bin/ 혹은
/usr/local/bin/ 아래에 둔다.

그리고 다시 실행하면 파이어폭스는 실행되지만 사이트에 연결할 수 없다는
창이 나온다면 geckodriver가 정상 동작한다는 뜻이다. 

selenium을 통해 실행할 django 프로젝트를 생성한다.

```python
$ django-admin startproject superlists
```

프로젝트가 생성되면 프로젝트 루트 디렉토리로 이동하여


```python
$ python manage.py runserver
```
를 실행하고 다른 터미널을 활용하여 다시 functional_test.py를
실행한다.


![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/selenium.png)


위와 같은 창이 나타난다면 django 기능 테스트를 위한 셀레니움 설정이
끝난 것이다.

<br/>
<br/>






