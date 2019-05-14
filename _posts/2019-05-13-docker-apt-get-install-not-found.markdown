---
layout: post
title: "Dockerfile build시 apt-get not found 에러 처리하기"
date: 2019-05-13 11:14:01 +0900
categories: [other]
encoding: UTF-8
---

<br>
<br>

근본적인 해결 방법은 찾지 못했고, 우회하는 방법 정도를 소개해드립니다.

<br>

### Dockerfile build시 apt-get not found 에러 처리하기

<br>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/dockerfile_error_01.png)

<br>

ubuntu:17.10을 베이스 이미지로 하여 Dockerfile을 빌드했을 때 RUN apt-get update 나 apt-get upgrade 명령어 실행시 

404  Not Found [IP: 91.189.88.161 80] 어쩌고 하는 에러를 볼 수 있다. 

이 문제의 원인은 빌드 캐싱과 연관이 있는데, 

RUN apt-get update 를 실행하더라도 가장 마지막에 실행됐던 이미지를 재사용하기 때문에 

당시 참조했던 소스 코드에 변경이 있는 경우 변경을 추적하지 못해서 발생한다. 

다시 말해, 과거에 실행했던 것을 그대로 사용하기 때문에 과거에 사용했던 라이브러리의 주소라던지, 버전 정보가 변경된 경우 NOT FOUND 에러를 뿜어낸다.

그러나 --no-cache 옵션을 추가해서 실행하여도 동일한 문제가 생기는걸로 봐선 추측이 잘못된 걸지도..

그래서 그냥 ubuntu의 버전을 변경하는 쪽으로 문제를 해결했다. 

ubuntu 18 버전 이상 혹은 17.12.0-ce 버전을 사용하면 문제 없이 빌드된다.


<br>
