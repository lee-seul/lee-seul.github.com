---
layout: post
title: "Data Structure, 02, Queue "
date: 2017-03-29 19:38:22 +0900
categories: [algorithm]
encoding: UTF-8
---

자료구조란 데이터를 효율적으로 이용할 수 있도록 저장하는 방법이며,
이를 통해 효율적인 알고리즘을 구현할 수 있다. 

자료구조는 선형구조와 비선형구조로 구분된다. 

<br/>


### 2. Queue

큐는 선형구조인 자료구조 중 하나이다. 스택과는 다르게 큐는 삽입과 삭제이 다른 곳에서 이루어 진다. 
큐는 먼저 삽입된 데이터가 가장 먼저 삭제된다.(First In, First Out: FIFO) 이때 데이터가 삽입되는 곳을 Rear, 
삭제되는 곳이 Front라고 부른다.

data삽입 순서 [data1, data2, data3, data4, data5]
data 삭제 순서 [data1, data2, data3, data4, data5]
![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/queue.png)


<br/>
<br/>

#### 2.1 ADT(Abstract Data Type)

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/queueADT.png)


<br/>
<br/>


#### 2.2 Python 구현

{% highlight python %}

# class로 구현

class Queue:
    def __init__(self):
        queue = [] # create()

    def is_empty(self):
        if not len(self.queue):
            return True
        return False
    
    def addQ(self, item):
        self.queue.append(item) 

    def deleteQ(self):
        if not self.is_empty():
            return self.queue.pop(0)


{% endhighlight %}

python 리스트 특성상 is_full은 구현할 필요가 없다. 

<br/>
<br/>


#### 2.3 순환 큐(Circular Queue)

Python에서 리스트를 사용해 구현할 때에는 상관없지만 일반적으로 큐는 삭제시에 비효율적이다. 툭히, C에서 배열을 활용해 큐를 구현할 경우, 삭제할 때마다 모든 데이터가 한칸씩 이동해야하는 상황이 생긴다. 이런 상황에서 큐가 커지면 커질수록 삭제 시마다 심각한 성능 저하가 발생할 수 있다. 이를 해결하기 위해
나온 것이 순환 큐이다. 

순환 큐는 일렬로 되어 있는 큐의 Front와 Rear를 연결하여 원형으로 만든 것을 생각하면 이해가 빠르다.

data삽입 순서 [data1, data2, data3, data4, data5]
data 삭제 순서 [data1, data2, data3, data4, data5]
![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/circular.png)

<br/>
<br/>

원형 큐가 처음 생성되어 비어있을 경우 front와 rear가 동일한 공간을 가르킨다. 그리고 데이터의 삽입이 이루어질 때마다 
rear는 바로 앞의 칸을 가르키고 데이터는 원래 rear가 가르키던 공간에 삽입된다. 반대로 데이터 삭제시 rear가 한칸 뒤를 가르키고
해당 값이 삭제된다.

<br/>
<br/>

#### 2.4 우선 순위 큐(Priority Queue)

우선 순위 큐는 데이터가 삽입된 순서가 아닌 우선 순위가 높은 데이터가 먼저 삭제(출력)된다. 우선 순위가 같은 데이터가 존재할 수 있으며
우선 순위를 판단하는 방법은 여러가지가 있다.

<br/>
<br/>




