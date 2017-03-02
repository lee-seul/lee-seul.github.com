--- 
layout: post
title: "Python collections module 01, Counter"
date:  2016-12-30 21:38:20 +0900
categories: [python, collections]
---

공식 문서: `https://docs.python.org/3/library/collections.html`

collections 내 자료구조 
namedtuple, deque, Counter, OrderedDict, defaultdict 등에 대한 내용 

### 1. Counter
리스트나 문자열에 포함된 요소들 각각의 갯수를 dictionary형태로 반환

{% highlight python %}

from collections import Counter

print(Counter(['aa', 'cc', 'dd', 'aa', 'bb', 'ee']))
print(Counter({"가":3, "나":2, "다":5}))
print(Counter(a=3, b=23, c=11, d=0))
print(Counter("Hellow hellow"))

{% endhighlight %}
 
#### 결과값 
```python
Counter({'aa': 2, 'ee': 1, 'bb': 1, 'cc': 1, 'dd': 1})
Counter({'다': 5, '가': 3, '나': 2})
Counter({'b': 23, 'a': 3, 'c': 11, 'd': 0})
Counter({'l': 4, 'o': 2, 'w': 2, 'e': 2, 'h': 1, ' ': 1, 'H': 1}) 
```

<br/>

#### update()
카운터 객체에 값을 추가할 때
{% highlight python %}

ct = Counter("abcde")
print(ct)

ct.update({'f':4, 'z': 100})
print(ct)

# 이런식으로도 가능
ct['x'] = 0
print(ct)

{% endhighlight %}

#### 결과값
```python
Counter({'c': 1, 'a': 1, 'b': 1, 'd': 1, 'e': 1})
Counter({'z': 100, 'f': 4, 'c': 1, 'a': 1, 'b': 1, 'd': 1, 'e': 1})
Counter({'z': 100, 'x': 0, 'f': 4, 'c': 1, 'a': 1, 'b': 1, 'd': 1, 'e': 1})
```

<br/>

#### elements()

{% highlight python %}

# 위에서 쓴 ct
print(list(ct.elements()))

{% endhighlight %}

#### 결과값
```python
['c', 'a', 'b', 'f', 'f', 'f', 'f', 'd', 'e', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z', 'z']
```

<br/>

#### most_common()
가장 많이 나온 값들의 시퀀스를 리턴

{% highlight python %}

print(ct.most_common())
print(ct.most_common(1))
print(ct.most_common(3))

{% endhighlight %}

#### 결과값
```python
[('z', 100), ('f', 4), ('c', 1), ('a', 1), ('b', 1), ('d', 1), ('e', 1)]
[('z', 100)]
[('z', 100), ('f', 4), ('c', 1)]
```

##### Counter 객체는 산술/집합 연산이 가능하다.(+, -, |, & 등)


