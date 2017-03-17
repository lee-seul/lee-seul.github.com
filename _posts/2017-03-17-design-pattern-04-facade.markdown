---
layout: post
title: "Design Patterns, 04, Facade "
date: 2017-03-17 21:48:22 +0900
categories: [concept, design-patterns]
encoding: UTF-8
---

디자인 패턴이란 자주 나타나는 시스템 구조를 조금 더 쉽고 빠르게 설계하기위해 재이용하기 좋은 형태로 
구조화한 것을 말한다.  

<br/>


### 4. Facade
퍼사드 패턴은 클래스와 클래스 간의 관계가 복잡하여 동작 방식에 대해 이해하기 어려울 때 이를 단순하게 만드는 디자인 패턴이다

즉, 단순화된 인터페이스를 통해 서브 시스템을 더 쉽게 사용하기 위해서 사용한다. (퍼사드 패턴을 사용했더라도 각각의 서브시스템에 직접 접근 할 수 없는 것은 아님)

퍼사드 패턴의 장점은 클라이언트 구현과 서브시스템을 분리할 수 있다. 예를 들어 서브시스템 A, B, C가 있을 때 
A에 변경점이 생기더라도 퍼사드 패턴에서는 B, C를 변경해주지 않아도된다. 


<br/>
<br/>

#### 4.1 구조

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/Facade.png)

위의 구조처럼 사용자는 파사드 클래스를 통해 서브 시스템을 사용하며, 서브 시스템의 내부 구조에 대한 이해가
필요하지 않다. 

<br/>
<br/>

#### 4.2 Python 예제




{% highlight python %}

class Subsystem1:
    def play(self):
        pass

class Subsystem2:
    def stop(self):
        pass

class Subsystem3:
    def pause(self):
        pass



class Facade:
    def __init__(self):
        self.one = Subsystem1()
        self.two = Subsystem2()
        self.three = Subsystem3()

    def exec(self):
        self.one.play()
        self.three.pause()
        self.two.stop()


if __name__ == "__main__":
    f = Facade()
    f.exec()

{% endhighlight %}

Facade클래스를 통해 Subsystem1, 2, 3을 사용한다. 


<br/>
<br/>

