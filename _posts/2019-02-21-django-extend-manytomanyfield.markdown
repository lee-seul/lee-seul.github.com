---
layout: post
title: "Django ManyToManyField 확장하기 "
date: 2019-02-21 10:39:03 +0900
categories: [django]
encoding: UTF-8
---

<br>
<br>

### Django ManyToManyField에 필드 확장하기

<br>

```python

class Tag(models.Model):
    name = models.CharField(max_length=100)


class Book(models.Model):
    title = models.CharField(max_length=100)
    tags = models.ManyToManyField(Tag)

```

<br>

위와 같은 모델이 있고 이미 마이그레이션이 적용되었는데, Book에 Tag가 추가된 날짜를 기록하고 싶다면 어떻게 해야할까?

처음부터 BookTag 형태와 같은 모델을 선언하고 ForeignKey로 Book과 Tag를 선언했다면 

간단하게 필드만 추가해주면 된다. 하지만 위와 같이 ManyToManyField로 선언한 경우 현재 상태로는 필드를 추가할 수 없다. 

이 때 사용할 수 있는 것이 through인데, ManyToManyField가 사용할 model을 지정할 수 있게 해준다. 

<br>

```python

class Tag(models.Model):
    name = models.CharField(max_length=100)


class Book(models.Model):
    title = models.CharField(max_length=100)
    tags = models.ManyToManyField(Tag, through='BookTag')


class BookTag(models.Model):
    book = models.ForeignKey(Book, on_delete=models.CASCADE)
    tag = models.ForeignKey(Tag, on_delete=models.CASCADE)

    class Meta:
        db_table = '생성된 ManyToMany Table 이름'

```
<br>

db_table에 들어갈 테이블의 이름은 django에서 자동으로 생성한 table의 이름으로 해준다. 

그런 다음 이미 테이블이 존재하므로 migrate --fake 를 실행하면, 기존에 있던 테이블을 BookTag 모델을 사용해서 접근할 수 있게 된다. 

<br>

```shell
$ python manage.py migrate --fake
```

<br>

여기에 필요한 필드만 추가해주면 끝이다. 


<br>


```python

class Tag(models.Model):
    name = models.CharField(max_length=100)


class Book(models.Model):
    title = models.CharField(max_length=100)
    tags = models.ManyToManyField(Tag, through='BookTag')


class BookTag(models.Model):
    book = models.ForeignKey(Book, on_delete=models.CASCADE)
    tag = models.ForeignKey(Tag, on_delete=models.CASCADE)
    created_at = models.DateTimeField(auto_now_add=True)    

    class Meta:
        db_table = '생성된 ManyToMany Table 이름'

```




<br>
<br>