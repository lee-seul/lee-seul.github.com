--- 
layout: post
title: "Python collections module 02, defaultdict"
date:  2017-01-11 23:38:21 +0900
categories: [python, collections]
---

공식 문서: `https://docs.python.org/3/library/collections.html`

collections 내 자료구조 
namedtuple, deque, Counter, OrderedDict, defaultdict 등에 대한 내용 

### 2. defaultdict
defaultdict는 default값을 지정할 수 있는 dictionary이다. dictionary의 경우 없는 키 값으로 
접근할 경우 에러를 출력하지만, defaultdict의 경우 지정한 default값을 출력한다.

defaultdict는 dictionary의 set_default라는 메쏘드를 가지고 있는데 set_default에 비해 사용이 편리하고 빠르다.


{% highlight python %}

# dict.set_default(key, default=None)
dic = {"a": 11, "bc": 9, "dd": 6}

print(dic.set_default("a", None))
print(dic.set_default("f", None))
{% endhighlight %}
 
#### 결과값
```python
11
None 
```

{% highlight python %}

from collections import defaultdict

dic = defaultdict(lambda: 0, a=10, b=11, c=13)

print(dic)
print(dic['a'])
print(dic['b'])
print(dic['c'])
print(dic['d'])
print(dic['f'])

{% endhighlight %}

#### 결과값
```python
defaultdict(<function <lambda> at 0x100556f28>, {'b': 11, 'a': 10, 'c': 13})
10
11
13
0
0
```

