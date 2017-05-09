---
layout: post
title: "Django Static File"
date: 2017-05-09 23:50:20 +0900
categories: [django]
encoding: UTF-8
---


Django 프로젝트를 배포해보면 정적 파일(Static file, Javascript, CSS, 이미지 등)이 개발할 때와는 다른 위치에서 
제공되어야 한다는 것을 알 수 있다. Django에서는 Web server와 Web Application Server(WAS)를 분리하기때문이다. 
같은 이유로 Django에서는 프로젝트를 배포할 시에 필요한 정적 파일 제공 기능을 가지고 있지 않다.  


<br/>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/django_static.png)


<br/>

Django는 기본적으로 위와 같은 구조를 가지게 된다. Web Server는 요청을 받아 정적 파일을 제공하고 WSGI라는 인터페이스를 통해 WAS와 통신한다. 
WAS는 동적으로 처리하는 기능을 제공하여 계속해서 변화하는 자원을 처리해줄 수 있다. 

<br/>

#### Static File, Media File


<br/>

Django는 Static file과 Media file 두 가지 종류의 정적 파일이 존재한다. Static file은 말 그대로 정적 파일로 Javascript, CSS, 이미지 파일 등을
말하며, Web Server를 통해 제공된다. 계속해서 변화하거나 추가되지 않고 고정되어 있다. 

Media file은 변하지 않는 다는 점은 정적 파일과 같으나 미리 준비될 수 없는 파일을 말한다. 사용자가 올린 이미지 파일 등에 이에 해당한다. Media file은 
WAS를 통해 제공된다.



<br/>
<br/>

