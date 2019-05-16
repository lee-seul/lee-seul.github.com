---
layout: post
title: "Docker build 소소한 속도 개선 팁"
date: 2019-05-16 11:22:02 +0900
categories: [other]
encoding: UTF-8
---

<br>
<br>



### Docker build 소소한 속도 개선 팁기

<br>

빌드 실행시 빌드가 실행되는 디렉토리 아래에 있는 모든 파일이 Docker 데몬으로 전송됩니다. 

파일이 많은 경우 데몬으로 전송하는 시간이 오래걸리고 전체 빌드 시간에 영향을 줍니다. 

따라서 빌드에 직접적으로 필요하지 않은 파일의 경우 디렉토리에서 제거하거나, 

.dockerignore라는 파일 안에 불필요한 파일의 파일명을 추가해줘야합니다. 

보통의 경우 Docker 빌드를 위한 파일은 별도 디렉토리에 Dockerfile과 함께 두는 것을 추천합니다.


<br>
