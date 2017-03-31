---
layout: post
title: "Data Structure, 03, Linked List "
date: 2017-03-31 16:31:22 +0900
categories: [algorithm, python]
encoding: UTF-8
---

자료구조란 데이터를 효율적으로 이용할 수 있도록 저장하는 방법이며,
이를 통해 효율적인 알고리즘을 구현할 수 있다. 

자료구조는 선형구조와 비선형구조로 구분된다. 

<br/>


### 3. Linked List

스택이나 큐와 같이 순차적으로 표현되는 자료구조의 경우 정해진 곳에서 삽입과 삭제가 이루어지기때문에 
데이터를 중간에 삽입하거나 중간에 있는 데이터를 삭제하기가 어렵다. 삭제가 일어났을 때도 데이터가 이동해야하는 
문제점이 있다. 연결 리스트는 이런 문제점을 해결할 수 있다. 

연결 리스트는 노드들로 구성되며, 노드는 0개 이상의 데이터 필드와 1개 이상의 링크를 포함한다. 


<br/>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/list01.png)


<br/>
<br/>

#### 3.1 ADT(Abstract Data Type)

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/listADT.png)


<br/>
<br/>


#### 3.2 Python 연결 리스트 구현

{% highlight python %}

# class로 구현
 
class Node:
    """링크드 리스트를 구성할 노드 클래스
    """
    def __init__(self, data, nextNode=None):
        self.data = data
        self.nextNode = nextNode


class LinkedList:
    """링크드 리스트
    """
    def __init__(self, data=None):
        if data is None:
            self.head = None
        else:
            self.head = Node(data)

    def is_empty(self):
        if self.head is None:
            return True
        return False
 
    def length(self):
       	result = 0
        temp = self.head
        while temp:
            temp = temp.nextNode
            result += 1
        return result

    def search(self, pos):
        cnt = 0
        temp = self.head
        while cnt != pos - 1:
            temp = temp.nextNode
            cnt += 1
        return temp

    def add(self, data, pos):
        temp = self.search(pos)
        new_node = Node(data, temp.nextNode)
        temp.nextNode = new_node

    def delete(self, pos):
        temp = self.search(pos)
        temp.nextNode = temp.nextNode.nextNode

    def display(self):
        temp = self.head
        while temp:
            print(temp.data, end=" ")
            temp = temp.nextNode

    

{% endhighlight %}



<br/>
<br/>


#### 3.3 원형 연결 리스트(Circular List)

일반적인 연결 리스트는 자료구조를 한번 순회한 이후에 시작점부터 다시 순회하여야 한다. 따라서 단순 연결 리스트는 
여러 차례 반복적인 순회를 실행하기에는 적합하지 않다. 그래서 단순 연결 리스트를 조금 변형하여 이 문제를 효과적으로
해결할 수 있는데 그 방법이 원형 연결 리스트이다. 


<br/>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/list02.png)

그림처럼 가장 마지막 노드의 링크 필드를 가장 첫 노드를 가르키게 하여 계속적으로 순회할 수 있다. 


<br/>
<br/>

#### 3.4 이중 연결 리스트(Doubly Linked List)

이중 연결 리스트는 단순 연결 리스트와는 다르게 이전 노드를 가르키는 링크 필드와 다음 노드를 가르키는 링크 필드로 구성되어 있다. 
즉, 양방향으로 연결되어 있어 양쪽 방향으로 탐색이 가능하다. 

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/list03.png)

<br/>
<br/>




