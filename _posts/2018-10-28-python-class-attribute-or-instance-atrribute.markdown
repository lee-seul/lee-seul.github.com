---
layout: post
title: "python class attribute, instance attribute"
date: 2018-10-28 23:26:33 +0900
categories: [python]
encoding: UTF-8
---

<br>
<br>

### Python 클래스 속성과 인스턴스 속성의 차이

<br>

```python

class Example:

    a = 1

    def __init__(self):
        self.b = 2

```

위와 같은 클래스가 있을 때 a와 같이 클래스의 바디에 있는 속성을 클래스 속성, 
__init__() 안에 있는 속성을 인스턴스 속성이라고 한다. 

<br>

인스턴스 속성의 경우 클래스에서 생성된 각 인스턴스가 각각의 값을 독립적으로 가지지만, 
클래스 속성의 경우 같은 클래스에서 생성된 모든 인스턴스에서 같은 값을 공유한다. 


오늘은 이런 차이가 생기는 이유를 알아보고자 한다. 

그러기 위해서 먼저 알아야 할 것은 import time과 runtime의 차이이다. 

<br>

### Import Time vs Runtime 

<br>

```python
# example.py

class Example:
    print('Import Time')
    a = 1

    def __init__(self):
        print('Runtime')
        self.b= 2

```

위의 코드를 실행해보면 
```python
$ python example.py
Import Time
```

위와 같은 결과가 나온다. Example에 대한 인스턴스가 생성되거나, 실행문이 없음에도 
Example 내부에 있는 print 문이 실행되는 것을 볼 수 있다. 

즉, 클래스 바디에 있는 값들은 임포트 시점에 한번만 실행되기 때문에 공통된 클래스에서 생성된 인스턴스는 모두 같은 값을 가질 수 밖에 없다. 

<br>

```python
# example.py

class Example:
    print('Import Time')
    a = 1

    def __init__(self, b):
        print('Runtime')
        self.b = b

example1 = Example(2)
example2 = Example(3)

print(example1.a, example1.b)
print(example2.a, example2.b)

```

이와 반대로 위의 코드를 실행하면 아래와 같은 결과가 나온다. 
```python
$ python example.py
Import Time
Runtime
Runtime
(1, 2)
(1, 3)
```

최초로 클래스 바디가 실행된 이후에 Example 클래스를 이용해서 2개의 인스턴스(example1, example2)를 생성하였지만, Example 바디에 있는 print문은 최초 1회만 실행되었다.

<br>

일반적으로 함수는 임포트 타임에 함수명을 바인딩하기는 하지만 함수 내부는 실행되지 않으며, 클래스의 경우 위의 예시처럼 클래스의 바디까지 실행되고 내부에 선언된 함수는 실행되지 않는다.




<br>
<br>