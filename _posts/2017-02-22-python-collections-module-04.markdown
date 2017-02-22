--- 
layout: post
title: "Python collections module 04-05, namedtuple, OrderedDict"
date:  2017-02-22 18:38:23 +0900
categories: [python, collections]
---

공식 문서: `https://docs.python.org/3/library/collections.html`

collections 내 자료구조 
namedtuple, deque, Counter, OrderedDict, defaultdict 등에 대한 내용 

### 4. namedtuple
namedtuple은 말그대로 이름을 가지는 tuple이다. tuple객체 자체와 tuple이 가지는 원소값 마다 이름을 가진다. 


{% highlight python %}

from collections import namedtuple

aa = {"Mark", 25, "남"}
bb = {"Alice", 23, "여"}

Person = namedtuple("Person", "name age gender") # tuple 이름을 Person, tuple이 가지는 원소의 이름을 name, age, gender로 설정

aa = Person(name="Mark", age="25", gender="남")
bb = Person(name="Alice", age="23", gender="여")

{% endhighlight %}
 

</br>
</br>

### 5. OrderedDict

**OrderedDict는 3.6에서 dictionary 구조가 바뀌면서 사라졌다.**
OrderedDict는 순서를 가지는 dictionary이다.

{% highlight python %}

from collections import OrderedDict

dic = OrderedDict() # 초기화

# 이후 사용법은 일반적인 dcitionary와 같음

dic[1] = "aaa"
dic[2] = "bbb"
dic[3] = "ccc"

for key, val in dic.items():
    print(key, value)

{% endhighlight %}

```python
1 aaa
2 bbb
3 ccc
```

