---
layout: post
title: "Data Structure, 06, Heap "
date: 2017-04-21 22:31:22 +0900
categories: [algorithm, python]
encoding: UTF-8
---

자료구조란 데이터를 효율적으로 이용할 수 있도록 저장하는 방법이며,
이를 통해 효율적인 알고리즘을 구현할 수 있다. 

자료구조는 선형구조와 비선형구조로 구분된다. 

<br/>


### 6. 힙(Heap)

<br/>
<br/>


힙은 완전 이진 트리에 있는 노드 중에서 키 값이 가장 크거(최대힙)나 가장 작은(최소힙) 노드를 찾기 위해 사용되는 자료구조이다. 
우선 순위 큐를 구현하기에 매우 좋은 자료구조이다.

최대힙(Max Heap)에서는 부모 노드의 키가 항상 자식 노드의 키 보다 커야한다. (최소힙은 그 반대)


<br/>

**최대힙(Max Heap)**

<br/>


![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/MaxHeap.png)

<br/>


<br/>

**최소힙(Min Heap)**

<br/>


![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/MinHeap.png)



<br/>
<br/>

#### 6.1 최대힙에서 삽입

<br/>

힙은 완전 이진 트리이므로 완전 이진 트리가 되도록 일단 최하단에 삽입한 이후 루트 쪽으로 비교하여 올라가는 식으로
이루어진다. 위의 최대힙에서는 7의 오른쪽 자식으로 일단 삽입된다.

<br/>

{% highlight python %}

class Node:

    def __init__(self, key, item):
        self.key = key
        self.item = item


class MaxHeap:

    def __init__(self):
        self.heap = [None]
        self.length = 0

    def push(self, node):  # 삽입
        self.length += 1
        idx = self.length
        if self.length != 1 and node.key > self.heap[idx//2].key:
            self.heap[idx] = self.heap[idx//2]
            idx //= 2

        self.heap.append(node)

    

{% endhighlight %}




<br/>
<br/>


#### 6.2 최대힙에서 삭제

<br/>

힙에서 삭제는 루트에서 이루어진다. 루트 노드를 일단 삭제한 이후 완전 이진 트리가 이루어지도록 구성해주면 된다.
이 때 힙에서 가장 마지막에 위치한 노드를 떼어내서 루트에 넣어준 다음 루트의 오른쪽 자식 노드와 왼쪽 자식 노드 중 큰 값을 
루트와 교체해준다. 만약 교체한 노드에 자식이 있을 경우 계속해서 비교하여 최대힙이 되도록 한다.

<br/>

{% highlight python %}

class MaxHeap:

    [....]

    def pop(self):
        if not self.length:
            return
        idx = self.length

        node = self.heap[1]
        temp = self.heap[idx]
        self.length -= 1
        parent = 1
        child = 2

        while child <= self.length:
            if child < self.length and self.heap[child].key < self.heap[child+1].key:
                   child += 1

            if temp.key >= self.heap[child].key:
                break

            self.heap[parent] = self.heap[child]
            parent = child
            child *= 2

        self.heap[parent] = temp
        return node

    

{% endhighlight %}

<br/>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/MaxHeap.png)

<br/>

위의 최대힙에서 삭제가 일어난다면, 9를 삭제한 이후 가장 마지막 노드인 2가 루트 자리에 삽입되고 루트의 자식 노드들인 8과 7 중 더 큰 값인 8과
교환이 일어난다. 교환된 2의 자식이 있으므로, 최대힙을 유지하기위해 5와 3중 큰 값을 2와 교체한다. 


최종 결과는 아래 그림과 같다.

<br/>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/pop.png)

<br/>
<br/>



<br/>
<br/>