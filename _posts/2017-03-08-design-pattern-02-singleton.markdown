---
layout: post
title: "Design Patterns, 02, Singleton "
date: 2017-03-08 21:13:31 +0900
categories: [concept, design-patterns]
encoding: UTF-8
---

디자인 패턴이란 자주 나타나는 시스템 구조를 조금 더 쉽고 빠르게 설계하기위해 재이용하기 좋은 형태로 
구조화한 것을 말한다.  

<br/>


### 2. Singleton

프로그램이 실행되는 중에 한 객체에 대해서 하나의 인스턴스만을 생성되도록 강제하는 디자인 패턴을 
싱글턴 패턴이라고 한다. 한 객체에서 만들어진 모든 인스턴스가 모든 데이터를 공유해서 사용한다. 

기본적인 구현 방법은 인스턴스가 생성될 때 이미 해당 객체에 인스턴스가 존재하는지 유무를 확인한 후
이미 생성되었다면 생성된 인스턴스를 반환하고, 그렇지 않으면 인스턴스를 생성한다. 

{% highlight python %}

# classic.py

class Singleton(object):
    def __new__(cls):
        if not hasattr(cls, 'instance'):
            cls.instance = super(Singleton, cls).__new__(cls)
        return cls.instance

{% endhighlight %}


```python
>>> from classic import Singleton 
>>>
>>> single = Singleton()
>>> another = Singleton()
>>> 
>>> single is another
True
>>> single.value = 'value'
>>> another.value
'value'
```

single과 another는 다른 인스턴스처럼 보이지만 single에서 value를 새로 할당했을 때에 
another에도 할당되는 것을 볼 수 있다. 


```python
>>>  class Child(Singleton):
...     pass
...
>>> child = Child()
>>> child is single
True
>>> child.value
'value'
```

Singleton을 상속받는 Child에서도 같은 현상이 일어남을 알 수 있다. 


```python
>>>  child.__dict__
{'value': 'value'}
>>> child.value2 = 'value'
>>> child.__dict__
{'value': 'value', 'value2': 'value'}
>>> single.__dict__
{'value': 'value', 'value2': 'value'}
>>> another.__dict__
{'value': 'value', 'value2': 'value'}
```

\_\_dict\_\_를 통해 데이터가 공유된다. 


---- 

<br/>
<br/>

Singleton을 사용하지 않았을 때에는 아래처럼 각 인스턴스가 각기 다른 데이터를 가지며, 같은 객체에서 
생성되었거나 상속되었다고 할지라도 데이터가 공유되지 않는다. 

```python
>>> class Test(obeject):
... pass
...
>>> class Test2(Test):
... pass
...
>>> t = Test()
>>> t2 = Test2()
>>>
>>> t.value = 'value'
>>> t.__dict__
{'value': 'value'}
>>> t2.__dict__
{}
>>> tt = Test()
>>> tt.__dict__
{}
```


Python 2.7에서는 python 3.x 처럼 사용하려면 


{% highlight python %}

class Singleton(object):
    _shared_data = {}
    def __new__(cls, *args, **kwargs):
        obj = super(Singleton, cls).__new__(cls, *args, **kwargs)
        obj.__dict__ = cls._shared_data
        return obj

{% endhighlight %}


<br/>
<br/>
