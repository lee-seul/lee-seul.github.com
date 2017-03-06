---
layout: post
title: "Design Patterns, 01, MVC "
date: 2017-03-07 00:43:31 +0900
categories: [concept, design-patterns]
encoding: UTF-8
---

디자인 패턴이란 자주 나타나는 시스템 구조를 조금 더 쉽고 빠르게 설계하기위해 재이용하기 좋은 형태로 
구조화한 것을 말한다.  

<br/>


### 1. MVC(Model View Controller)


MVC는 애플리케이션 구조를 Model, View, Controller 세 부분으로 나눈 것으로 Model은 데이터를 저장하고 View는 UI, Controller는 View와 Model을 연결한다. 

MVC패턴에서 View와 Controller는 Model에 의존적이지만 Model은 독립적인 특성을 가진다.

MVC의 예로는 Django(MVC와 유사한 MTV를 사용)가 있다. 



![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/MVC_pattern.png)



#### Model

Model은 데이터와 데이터가 동작하는 방식을 제공하는 부분이다. Model은 데이터를 생성하고 이를 DB와 연결해주는 
인터페이스를 제공한다. 또한 데이터 생성 및 저장시에 데이터를 검증하여 데이터의 무결성을 유지한다. 


#### View

View는 쉽게 말해 유저가 보는 UI를 말한다. 대부분의 로직은 Controller단에서 처리하기때문에 View에는 매우 간단한 로직이 포함되거나 포함되지 않아야한다. 


#### Controller

Controller는 어플리케이션의 로직을 담당하는 부분이다. Model에서 받은 데이터를 가공하여 View로 전달하며, 사용자 요청을 처리한다. 



#### MVC패턴의 이점

Model, Controller, View의 세부분으로 나누어 시스템을 이해하기 쉬워지며, 업무 분담시 효율적으로 업무를 분배할 수 있다. 유지보수 시에도 전체 시스템을 수정할 필요 없이 해당 부분만을 수정할 수 있어 매우 실용적이다. 








