--- 
layout: post
title: "Python collections module 03, deque"
date:  2017-01-22 17:43:26 +0900
categories: [python, collections]
---

공식 문서: `https://docs.python.org/3/library/collections.html`

collections 내 자료구조 
namedtuple, deque, Counter, OrderedDict, defaultdict 등에 대한 내용 

### 3. deque
deque는 양방향 큐(혹은 스택)이다. 자료구조의 앞 뒤에서 원소를 추가하거나 뺄 수 있는 형태의 자료구조이다.
기존 큐에서는 한쪽 방향에서 삭제, 다른 방향에서 추가가 이루어지는데 deque경우는 양방향에서 자료의 추가 및 삭제가 가능하다

{% highlight python %}

from collections import deque

deq1 = deque("Hello Python")
deq2 = deque(["a", "b", "d", "a", "c"])

print("deq1 =>", deq1)
print("deq2 =>", deq2)

print(len(deq1))
print(deq1[0])
print(deq1[-1])

{% endhighlight %}
 

```python
deq1 => deque(['H', 'e', 'l', 'l', 'o', ' ', 'P', 'y', 't', 'h', 'o', 'n'])
deq2 => deque(['a', 'b', 'd', 'a', 'c'])
```


기본적으로 list와 같은 접근이 가능
{% highlight python %}

print(len(deq1))
print(deq1[0])
print(deq1[-1])

deq1.remove('o')
print(deq1)

deq1.append('aaa')
print(deq1)

{% endhighlight %}


```python
12
H
n
deque(['H', 'e', 'l', 'l', ' ', 'P', 'y', 't', 'h', 'o', 'n'])
deque(['H', 'e', 'l', 'l', ' ', 'P', 'y', 't', 'h', 'o', 'n', 'aaa'])
```

popleft(), appendleft()


{% highlight python %}

deq1.appendleft('qqqq')
print(deq1)

print(deq1.popleft())
print(deq1)

print(deq1.pop())
print(deq1)

{% endhighlight %}


```python
deque(['qqqq', 'H', 'e', 'l', 'l', ' ', 'P', 'y', 't', 'h', 'o', 'n', 'aaa'])
qqqq
deque(['H', 'e', 'l', 'l', ' ', 'P', 'y', 't', 'h', 'o', 'n', 'aaa'])
aaa
deque(['H', 'e', 'l', 'l', ' ', 'P', 'y', 't', 'h', 'o', 'n'])
```

extend(), extendleft()

{% highlight python %}

deq1.extend('abcd')
print(deq1)

deq1.extendleft('xyz')
print(deq1)

{% endhighlight %}

```python
deque(['H', 'e', 'l', 'l', ' ', 'P', 'y', 't', 'h', 'o', 'n', 'a', 'b', 'c', 'd'])
deque(['z', 'y', 'x', 'H', 'e', 'l', 'l', ' ', 'P', 'y', 't', 'h', 'o', 'n', 'a', 'b', 'c', 'd'])
```