---
layout: post
title: "Data Structure, 01, Stack "
date: 2017-03-28 14:58:22 +0900
categories: [algorithm]
encoding: UTF-8
---

자료구조란 데이터를 효율적으로 이용할 수 있도록 저장하는 방법이며,
이를 통해 효율적인 알고리즘을 구현할 수 있다. 

자료구조는 선형구조와 비선형구조로 구분된다. 

<br/>


### 1. Stack

스택은 선형구조 중 하나로, 삭제과 삽입이 한 곳에서 이루어지는 구조를 말한다. 이름처럼 데이터가 쌓여가는 구조이기때문에 가장 나중에 삽입된
값이 가장 먼저 삭제된다.(Last in Fist Out: LIFO)

data삽입 순서 [data1, data2, data3, data4, data5]
data 삭제 순서 [data5, data4, data3, data2, data1]
![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/stack_small.png)


<br/>
<br/>

#### 1.1 ADT(Abstract Data Type)

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/stackADT_small.png)


<br/>
<br/>


#### 1.2 Python 구현

{% highlight python %}

# class로 구현

class Stack:
    def __init__(self):
        stack = [] # create()

    def is_empty(self):
        if not len(self.stack):
            return True
        return False
    
    def push(self, item):
        self.stack.append(item) 

    def pop(self):
        if not self.is_empty():
            return self.stack.pop()


{% endhighlight %}

python 리스트 특성상 is_full은 구현할 필요가 없다. 

<br/>
<br/>


#### 1.3 Stack 예제

##### 1918번

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/stack01.png)

<br/>

스택으로 풀 수 있는 문제 중 대표적인 것이 중위 표기식을 후위 표기식으로
바꾸는 문제이다. 

<br/>

{% highlight python %}

# 리스트 특성 활용

op_order = {"-": 1, "+": 1, "*": 2, "/": 2, "(": 0}

ex = input()
stack = []

for c in ex:
    if c in op_order.keys():
        if not stack or c == '(':
            stack.append(c)
        else:
            while stack and op_order[c] <= op_order[stack[-1]]:
                print(stack.pop(), end='')
            stack.append(c)
    
    elif c == ')':
        while stack and stack[-1] != '(':
            print(stack.pop(), end='')

        if stack and stack[-1] == '(':
            stack.pop()

    else:
        print(c, end='')     

while stack:
    print(stack.pop(), end='')


{% endhighlight %}



<br/>
<br/>


##### 1874번

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/stack02.png)

<br/>

{% highlight python %}

# 리스트 특성 활용

import sys  
write = sys.stdout.write # print()보다 빠름
read = sys.stdin.readline # input()보다 빠름

n = int(input())

stack = []
input_list = []
result = []

for _ in range(n):
    val = int(read())
    input_list.append(val)

u = True
index = 0
i = 1
while index < len(input_list):
    if i > input_list[index]:
        if stack[-1] == input_list[index]:
            stack.pop()
            result.append("-")
            index += 1
        else:
            u = False
            break
    else:
        stack.append(i)
        result.append("+")
        i += 1

if u:
    for s in result:
        write(s+"\n")
else:
    print("NO")


{% endhighlight %}



<br/>
<br/>



