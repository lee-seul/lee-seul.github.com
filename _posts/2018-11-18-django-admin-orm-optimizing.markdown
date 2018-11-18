---
layout: post
title: "Django Admin에서 select_related 적용하기"
date: 2018-11-18 13:04:03 +0900
categories: [django]
encoding: UTF-8
---

<br>
<br>

### Django Admin에서 select_related 적용하기

<br>

일반적으로 django admin에서는 쿼리가 비효율적으로 실행될 수 있는 부분이 많이 존재한다. 


```python
from django.db import models


class Author(models.Model):
    name = models.CharField(max_length=10)


class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)

```

<br>

예를 들어 위와 같은 모델이 있다고 가정했을 때 Book Admin에서 Book의 저자 정보까지 표시해주려고 하면

아래와 같이 구현할 수 있다. 


```python
from django.contrib import admin


class BookAdmin(admin.ModelAdmin):
    list_display = ('id', 'title', 'author_name', )

    def author_name(self, book):
        return book.author.name
    author_name.short_description = '저자명'


```

일반적으로는 위와 같이 구현되는데, Book 모델이 복잡해져서 author_name과 같은 형태의 메소드가 많이 생기게 되면 

Admin페이지가 느려지는 것을 경험할 수 있다. 

위와 같은 코드가 문제가 되는 이유는, Book 모델을 전부 가져온 다음에 각 Book 모델에 대해 author_name이 실행되기때문에 Book 모델이 많아지면 많아질수록 실행되는 쿼리의 수가 늘어나기 때문이다. 

즉, Book이 100개의 row를 가지고 있다면, book.author.name 코드를 통해 DB에서 book에 해당 하는 author의 

name을 찾는 쿼리가 100번 실행된다. (N+1 문제)


N+1 문제는 ORM을 사용하는 어디에서나 발생할 수 있는데 select_related이나 prefetch_related를 통해 해결할 수 있다. 

Book, Author와 같은 형태(1:N) 관게에서는 Book.objects.all().select_related('author')와 같은 형태로 적용한다. 

Admin에서는 2가지 방법을 통해서 2가지 문제를 해결 할 수 있다. 


1. list_select_related 사용
2. get_queryset 오버라이딩 


<br>

1번의 경우 ModelAdmin 내부에 list_select_related에 select_related 하고 싶은 필드를 추가해주기만 하면된다.

```python
from django.contrib import admin


class BookAdmin(admin.ModelAdmin):
    list_display = ('id', 'title', 'author_name', )
    list_select_related = ['author', ]

    def author_name(self, book):
        return book.author.name
    author_name.short_description = '저자명'

```


<br>

2번의 경우 get_queryset 메소드를 오버라이딩 하여 해당 모델의 queryset을 얻어 올 때부터 
select_related와 prefetch_related를 적용시키면 된다. 

```python

class BookAdmin(admin.ModelAdmin):
    list_display = ('id', 'title', 'author_name', )
    list_select_related = ['author', ]

    def get_queryset(self, request):
        ordering = self.get_ordering(request)
        if ordering:
            qs = qs.order_by(*ordering)
        return qs.select_related('author')

    def author_name(self, book):
        return book.author.name
    author_name.short_description = '저자명'

```




<br>
<br>