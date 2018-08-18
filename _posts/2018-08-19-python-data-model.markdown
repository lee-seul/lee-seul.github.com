---
layout: post
title: "Python 데이터 클래스(data class), 데이터 모델"
date: 2018-08-19 02:22:12 +0900
categories: [python]
encoding: UTF-8
---

<br>
<br>


#### 데이터 클래스?

데이터 클래스 혹은 데이터 모델, 객체 모델이라고 불리는데 python을 좀 더 python스럽게 사용할 수 있도록 도와주는 
일종의 프레임워크이다. 

반복이나 시퀀스의 슬라이싱 등에 대한 인터페이스 역할을 하는데 특별 메소드(매직 메소드, 던더 메소드)라고 불리는 메소드에 의해서 구현된다. 


<br>

python을 사용하다보면 모든 것이 객체라고 하면서 시퀀스의 길이를 측정할 때 my_list.len()이 아니라 len()이라는 내장 함수를 사용하는 것을 볼 수 있다. 

이는 언어자체에서 일관성을 제공하기 위함인데, 덕분에 몇가지 특별 메소드만 선언해주어도 python에서 제공하는 많은 내장 함수들의 기능들을 사용할 수 있게 된다.

<br>

예를 들어 특정 클래스에서 python 시퀀스와 비슷한 동작을 원한다면 \_\_len__(), \_\_getitem__() 등을 클래스에 메소드로 선언만 해주면 된다. 

<br>

```python
# python 3.6

class MyList:

    def __init__(self):
        self._data = []

    def __len__(self):
        return len(self._data)

    def __getitem__(self, idx):
        return self._data[idx]

```

이런 식으로 두가지 특별 메소드만 선언해주어도 인덱스를 사용하는 대부분의 기능들(인덱스에 의한 접근, 슬라이싱 등)을 사용할 수 있게 된다.

<br>
<br>


python에는 \_\_iter__(), \_\_add__(), \_\_call__(), \_\_str__() 등 많은 숫자의 특별 메소드가 정의되어 있어 필요해 따라 클래스 내부에 선해주기만 하면 된다. 

<br>

#### Python 3.7에서의 데이터 모델 

<br>

특별 메소드로 인해 편리한 점은 많지만 기능에 따라 많은 양의 특별 메소드를 추가해야 하는 일이 생길 수 있는데 
python 3.7에 dataclass 라는 모듈이 추가되면서 매우 편리하게 데이터 모델을 사용할 수 있게 되었다. 

<br>

```python

from dataclass import dataclass


@dataclass(order=True)
class Sample:   
    ...

```

<br>

위의 코드처럼 order=True만을 추가해주는 것으로 \_\_lt__(), \_\_le__(), \_\_gt__(), \_\_ge__() 가 자동으로 생성된다. dataclass에는 order 이 외에도 다양한 인자를 넘길 수 있다.




<br>
<br>
<br>
