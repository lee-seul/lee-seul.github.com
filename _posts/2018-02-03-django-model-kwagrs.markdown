---
layout: post
title: "Django model에서 **kwagrs 사용하기"
date: 2018-02-03 17:27:11 +0900
categories: [django, backend]
encoding: UTF-8
---

<br>


### **kwargs?

<br>

**kwargs는 키워드된 n개의 변수들을 함수의 인자로 보낼 때 사용한다. (*args 키워드되지않은 n개의 변수)

{% highlight python %}

def print_value(A, B, C):
    value = "{}/{}/{}".format(A, B, C)
    print(value)


args = (1, 2, 3)
kwargs = {"A": 5, "B": 11, "C": 99}


print_value(*args) # 1/2/3

print_value(**kwagrs) # 5/11/99


{% endhighlight %}

<br>

*나 **는 패킹, 언패킹과도 관련이 있는데 위처럼 리스트나 딕셔너리 앞에 쓰면 언패킹, 흩어져있는 값들을 *args, **kwagrs로 묶는 다면 패킹이라고 한다. 

<br>

위처럼 *args나 **kwargs는 코드 전반에서 유용하게 쓰일 수 있는데 django 코드를 직접봤거나 공식 문서를 자세히 보았다면 여기저기서 쓰이고 있다는 것을 알 수 있다. 

그 중에서 model에서 **kwargs를 사용하는 방법을 정리해보았다.


<br>

### django model에서 **kwagrs 사용하기

<br>

django 공식 문서의 Queryset API 부분을 보면

- filter(**kwagrs)
- exclude(**kwagrs)
- annotate(*args, **kwagrs)
- get(**kwagrs)
- create(**kwagrs)
- get_or_create(defaults=None, **kwargs
- update_or_create(defaults=None, **kwargs)
- aggregate(*args, **kwargs)
- update(**kwargs)

등 delete()를 제외한 대부분의 model manager method에서 **kwargs를 사용하고 있다는 점을 알 수 있다.


<br>

{% highlight python %}

from django.db import models


class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=20)

    def __str__(self):
        return self.title


{% endhighlight %}

위와 같은 모델이 있을 때 **kwargs를 실제로 사용한다면

<br>
<br>

{% highlight python %}

from .models import Book


kwargs = {'title': 'AAA', 'name': 'Seul'}

Book.objects.create(**kwargs) 

Book.objects.filter(**kwargs) 


{% endhighlight %}


위와 같이 사용이 가능하다. 


<br>


