---
layout: post
title: "Design Patterns, 05, Proxy "
date: 2017-03-20 21:48:22 +0900
categories: [concept, design-patterns]
encoding: UTF-8
---

디자인 패턴이란 자주 나타나는 시스템 구조를 조금 더 쉽고 빠르게 설계하기위해 재이용하기 좋은 형태로 
구조화한 것을 말한다.  

<br/>


### 5. Proxy
프록시(proxy)는 대리자라는 뜻을 가진 단어로, 
특정 객체에 대한 접근을 효울적으로하기 위한 대리 객체를 활용하는 디자인 패턴이다. 
주로 사용되는 경우는 게으른 초기화, 로깅 구현, 네트워크 연결, 참조 카운트 구현 등이 있다. 

사용하는 데에 리소스가 많이 필요한 객체의 경우, 요청이 들어왔을 때마다 새로운 객체를 생성하는 것이 아니라
기존에 생성 해놓은 객체를 사용하고 새로운 참조를 만들어 사용한다. 또 사용이 끝난 이후에는 빠르게 메모리를
정리해주는데 이때 객체의 참조 카운트 역할을 하는 것이 프록시이다. 



<br/>
<br/>

#### 5.1 구조

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/proxy.png)

위 다이어그램에서 Proxy 객체는 실제 사용할 객체인 RealSubject객체와 동일 인터페이스를 상속받고 
Client는 이 Proxy 객체에 RealSubject에 대한 호출을 위임한다. 

프록시 디자인 패턴은 자주 사용하는 객체를 캐싱함으로써 애플리케이션 성능을 향상시킬 수 있고,
프록시 객체에서 접근 권한 통제를 통해 보안 수준을 높일 수 있다. 

<br/>
<br/>

#### 5.2 Python 예제




{% highlight python %}

class SubjectInterface:
    def add(self, a, b):
        pass

    def sub(self, a, b):
        pass


class RealSubject(SubjectInterface):
    def add(self, a, b):
        return a + b

    def sub(self, a, b):
        return a - b


class ProxyClass(SubjectInterface):
    def __init__(self):
        self.subject = RealSubject()

    def add(self, a, b):
        return self.subject.add(a, b)

    def sub(self, a, b):
        return self.subject.sub(a, b)



if __name__ == "__main__":
    """프록시 객체
    """
    p = ProxyClass()
    print(p.add(100, 25)) # 125
    print(p.sub(100, 25)) # 75

{% endhighlight %}



<br/>
<br/>

