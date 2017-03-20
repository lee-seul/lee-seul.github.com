---
layout: post
title: "Design Patterns, 06, Observer "
date: 2017-03-20 23:48:22 +0900
categories: [concept, design-patterns]
encoding: UTF-8
---

디자인 패턴이란 자주 나타나는 시스템 구조를 조금 더 쉽고 빠르게 설계하기위해 재이용하기 좋은 형태로 
구조화한 것을 말한다.  

<br/>


### 6. Observer
옵저버(관찰자)라는 뜻을 가진 만큼 특정 객체를 관찰하는 객체가 존재하고 관찰하는 객체를 통해 
관찰되는 객체의 정보가 필요한 모든 객체에 변화된 정보를 전달한다. 

일반적으로 일대다 관계 활용을 위해 사용한다. 

발행-구독 관계로 설명이 가능한데 신문사는 관찰되는 객체, 구독자는 정보를 받는 대상들이다. 이 때 구독자와
신문사가 발행한 신문을 연결해주는 매개체인 배달부가 옵저버 객체이다. 생산된 정보가 있을 때, 그 정보를 필요로
하는 객체들에 정보를 전달해주는 역할을 한다. 



<br/>
<br/>

#### 6.1 구조

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/observer.png)



<br/>
<br/>

#### 6.2 Python 예제



{% highlight python %}



class Observer: 
    """옵저버 클래스
    """
    def __init__(self):
        self.subscriber = []
        self.msg = ""

    def notify(self):
        for sub in self.subscriber:
            sub.msg = self.msg

    def register(self, obsv):
        self.subscriber.append(obsv)

    def unregister(self, obsv):
        self.subscriber.remove(obsv)


class subscriber:
    """옵저버를 통해 정보를 전달 받는 객체들
    """
    def __init__(self):
        msg = ""

    def update(self):
        print(self.msg)


class Subject:
    """옵저버에 의해 관찰되는 객체
    """
    def __init__(self):
        self.observer = []

    def notify_observer(self, info):
        for obsv in self.observer:
            obsv.msg = info

    def attach(self, obsv):
        self.observer.append(obsv)

    def dettach(self, obsv):
        self.observer.remove(obsv)


if __name__ == "__main__":
    a = subscriber() 
    b = subscriber()
    c = subscriber()
    
    ob = Observer() # 옵저버 객체 생성
    ob.register(a)  # 옵저버에 객체 등록
    ob.register(b)
    ob.register(c)

    sub = Subject() # 서브젝트 객체에 옵저버 등록
    sub.attach(ob)
    
    sub.notify_observer("Hello")

    ob.notify()
    
    a.update()
    b.update()
    c.update()


    

{% endhighlight %}



<br/>
<br/>

