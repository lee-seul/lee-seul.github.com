---
layout: post
title: "Python collections module"
date:  2016-12-30 21:38:20 +0900
categories: [python]
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

{% endhighlight %}

```python
Counter({'aa': 2, 'ee': 1, 'bb': 1, 'cc': 1, 'dd': 1})
Counter({'다': 5, '가': 3, '나': 2})
Counter({'b': 23, 'a': 3, 'c': 11, 'd': 0})
```

