---
layout: post
title: "Django model ForeinkeyField on_delete"
date: 2018-01-28 17:21:23 +0900
categories: [django, backend]
encoding: UTF-8
---

<br>


### **Django model Foreign key Field on_delete 종류**

<br>

django에서 모델 구현시 ForeignKeyField를 사용할 일이 매우 많은데
DB상에서 참조무결성을 유지하기위하여 ForeignKeyField가 바라보는 값이 
삭제될 때 어떻게 처리해줄지 미리 옵션으로 줄 수 있다.

<br>

#### 1. CASCADE


ForeignKeyField가 바라보는 값이 삭제될 때 ForeignKeyField를 포함하는 모델 인스턴스(row)도 삭제된다. 

<br>

{% highlight python %}

from django.db import models


class TestModel(models.Model):
    pass


class FKModel(models.Model):
    test = models.ForeignKeyField(TestModel, on_delete=models.CASCADE)

    # skip


{% endhighlight %}

<br>



#### 2. PROTECT


ForeignKeyField가 바라보는 값이 삭제될 때 삭제가 되지않도록 
 ProtectedError를 발생시킨다. 

<br>

{% highlight python %}

from django.db import models


class TestModel(models.Model):
    pass


class FKModel(models.Model):
    test = models.ForeignKeyField(TestModel, on_delete=models.PROTECT)

    # skip


{% endhighlight %}

<br>


#### 3. SET_NULL


ForeignKeyField가 바라보는 값이 삭제될 때 ForeignKeyField값을 null로 바꾼다. (null=True일 때만 가능)

<br>

{% highlight python %}

from django.db import models


class TestModel(models.Model):
    pass


class FKModel(models.Model):
    test = models.ForeignKeyField(TestModel, on_delete=models.SET_NULL, null=True)

    # skip


{% endhighlight %}

<br>


#### 4. SET_DEFAULT


ForeignKeyField가 바라보는 값이 삭제될 때 ForeignKeyField값을 default 값으로 바꾼다. (default값이 있을 때만 가능)

<br>

{% highlight python %}

from django.db import models


class TestModel(models.Model):
    pass



DEFAULT_TEST_MODEL_PK = 1


class FKModel(models.Model):
    test = models.ForeignKeyField(TestModel, on_delete=models.SET_DEFAULT,default=DEFAULT_TEST_MODEL_PK)

    # skip


{% endhighlight %}

<br>


#### 5. SET()


ForeignKeyField가 바라보는 값이 삭제될 때 ForeignKeyField값을 SET에 설정된 함수 등에 의해 설정된다.

<br>

{% highlight python %}

from django.db import models


class TestModel(models.Model):
    title = models.CharField(max_length=100)

    # skip

def set_FK_Model_test():
    return TestModel.objects.get(id=1)


class FKModel(models.Model):
    test = models.ForeignKeyField(TestModel, on_delete=models.SET(set_FK_Model_test))

    # skip


{% endhighlight %}

<br>


#### 6. DO_NOTHING


ForeignKeyField가 바라보는 값이 삭제될 때 아무런 행동을 취하지 않는다. 
참조무결성을 해칠 위험이 있다 

<br>

{% highlight python %}

from django.db import models


class TestModel(models.Model):
    pass


class FKModel(models.Model):
    test = models.ForeignKeyField(TestModel, on_delete=modelsDO_NOTHING)

    # skip


{% endhighlight %}

<br>



<br>
<br>