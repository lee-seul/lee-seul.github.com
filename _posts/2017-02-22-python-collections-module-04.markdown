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
 

#### _replace() 활용 dictionary를 namedtuple로 바꾸기

{% highlight python %}

from collections import namedtuple

def dict_to_tuple(dic):
    """
    dictionary를 받아서 namedtuple로 변환하는 함수
    
    dic: dictionary
    """
    return prototype_people._replace(**dic) # people의 프로토타입 튜플과 _replace()를 활용  


people = namedtuple("People", ["namne", "age", "sex"]) # 변환할 dictionary의 key 값들과 매칭될 수 있는 변수명들을 준다. 
prototype_people = people("", 0, "") # 값이 없는 people named tuple 만들기

test_dic = {"name": "lee", "age": 27, "sex": "M"}
print(dict_to_tuple(test_dic))

{% endhighlight %}


#### 결과값
```python
# dict_to_tuple의 결과값 
People(name="lee", age=27, sex="M")
```


<br/>
<br/>

### 5. OrderedDict

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

#### 결과값
```python
1 aaa
2 bbb
3 ccc
```


<br/>
<br/>
