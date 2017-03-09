---
layout: post
title: "Design Patterns, 03, Factory "
date: 2017-03-09 19:48:22 +0900
categories: [concept, design-patterns]
encoding: UTF-8
---

디자인 패턴이란 자주 나타나는 시스템 구조를 조금 더 쉽고 빠르게 설계하기위해 재이용하기 좋은 형태로 
구조화한 것을 말한다.  

<br/>


### 3. Factory

객체지향 프로그래밍에서 상속을 사용할 경우 코드의 재사용성이 증대되고, 다형성을 활용할 수 있게되어 효율적으로 
객체 그룹을 컨트롤할 수 있게 된다. 하지만 상속을 남용할 경우 몇가지 문제점이 생기게 된다. 

<br/>
<br/>

#### 3.1 구성(Composition, 조립)

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/Factory_01.png)

위와 같은 구조의 프로그램이 있을 때(BaseMacBookPro의 자식 클래스가 무수히 많다고 가정), MacBookPro16으로 인해 문제가 발생한다. MacBookPro16의 경우 터치바와 관련된 메소드를 포함하고 있는데 이 때문에 부모 클래스에도 터치바와 관련된 메소드를 추가해주어야하고 추가된 이후에 터치바와 관련이 없는 클래스에도 터치바 관련 메소드가 상속되어진다. 상속 받는 클래스의 숫자가 많으면 많을 수록 문제가 복잡해진다.

상속은 매우 유용하지만 부모의 public한 속성과 메소드를 모두 상속받아야하기 때문에 자식 클래스의 일부 특성으로 인해 문제가 발생할 수 있는데 이런 문제를 해결하기위한 몇 가지 방법 중에 구성을 활용하는 방법이다.


구성이란 보통 필드에서 다른 객체를 참조하는 방식으로 구현되며, 구현과 런타임 구조가 복잡해진다는 단점을 가지지만 코드의 유연성면에서 단점을 상쇄시킬 정도의 장점을 가지고 있다. 
따라서 상속보다는 구성으로 설계하는 것을 우선 고려해야한다.

<br/>
<br/>

#### 3.2 Simple Factory


![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/Factory_02.png)

구성을 이용했을 때 클래스 다이어그램

{% highlight python %}

class TouchBarInterface(object):
    def print_model(self):
        pass


class TouchBar(TouchBarInterface):
    def print_model(self):
        print(u"터치바, 13인치")


class NoTouchBar(TouchBarInterface):
    def print_model(self):
        print(u"13인치")


class NoTouchBar15(TouchBarInterface):
    def print_model(self):
        print(u"15인치")
    

class TouchBar15(TouchBarInterface):
    def print_model(self):
        print(u"터치바, 15인치")


class BaseMacBookPro(object):
    _model = None
    def __init__(self, modelType:TouchBarInterface) -> None: # type hint
        self._model = modelType

    def setModel(self, modelType:TouchBarInterface) -> None: # type hint
        self._model = modelType

    def modelFunction(self):
        self._model.print_model(self._model)

if __name__ == "__main__":
    macbook1 = BaseMacBookPro(NoTouchBar15)
    macbook1.modelFunction()

    macbook2 = BaseMacBookPro(TouchBar)
    macbook2.modelFunction()

    macbook1.setModel(NoTouchBar)
    macbook1.modelFunction()



{% endhighlight %}

##### **결과값**:
```python
15인치
터치바, 13인치
13인치
```

BaseMacBookPro라는 Factory객체를 활용하여 여러 객체를 생성할 수 있다. 생성할 클래스를 파라미터로
할당받아 필요한 클래스를 생성해준다. 

<br/>
<br/>

#### 3.3 Factory Method
Simple Factory와 다른 점은 만들어지는 객체의 종류를 서브클래스에서 결정한다는 점입니다. 

{% highlight python %}

class MacBookFactory(object):
    def createMacbook(model):
        pass

class ConreateMacBookFactory(MacBookFactory):
    def createMacbook(model):
        if model == "TouchBar15":
            return TouchBar15()

        elif model == "NoTouchBar":
            return NoTouchBar()

                    .
                    .
                    .
                    .


{% endhighlight %}

원래의 팩토리 클래스에서는 어떤 클래스가 만들어지는지 알 수 없고, 서브 클래스에서 어떤 클래스를 만들지
결정하게함으로써 객체 생성을 캡슐화한다. 


<br/>
<br/>

#### 3.4 Absract Factory
실제(concrete) 클래스 등에 의존하지 않고 객체를 생성하는 패턴을 말한다. 실제 클래스까지 서브 클래스에서 생성한다. 

