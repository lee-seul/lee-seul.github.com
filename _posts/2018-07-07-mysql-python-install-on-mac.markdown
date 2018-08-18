---
layout: post
title: "MySQL-python OSX에 설치시 오류 대응"
date: 2018-07-07 20:53:24 +0900
categories: [python]
encoding: UTF-8
---

<br>
<br>

다른 프로젝트의 환경 세팅을 하던 중 MySQL-python를 설치하는 부분에서 아래와 같은 에러가 나는 것을 발견했다. 


![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/mysql-python.png)

<br>

찾아보니 MySQL-python은 MySQL 5.5 이하 버전을 지원하는데 brew install mysql 명령어는 최신 버전을 받아와서 생긴 이슈였다. 

<br>

해결법은 간단하다. brew를 통해 mysql 5.5를 설치해주면 된다. 



```shell
$ brew install mysql@5.5
```

<br>

그런 다음 설치하면 정상적으로 설치된다.(이번 경우에는)


<br>

MySQL-python은 python3를 지원하지 않으며 2014년 1월 2일이 배포된 버전이 최신 버전이므로 PyMySQL이나 다른 MySQL connector를 사용하기 바란다. 

<br>
<br>
