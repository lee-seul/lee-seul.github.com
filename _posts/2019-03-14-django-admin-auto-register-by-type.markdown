---
layout: post
title: "type을 이용한 동적 클래스 생성 및 Django admin 자동 등록 "
date: 2019-03-14 11:34:57 +0900
categories: [django]
encoding: UTF-8
---

<br>
<br>

### type을 이용한 동적 클래스 생성 및 Django admin 자동 등록 

<br>

python에는 type이라는 함수(사실은 클래스)가 있는데, 주로 자료형을 확인하기 위해 사용되지만 

type을 통해 클래스를 동적으로 생성할 수 있다. 

사용법은 간단하다. 클래스의 이름, 상속 받을 클래스, 클래스의 바디를 구성할 값들을 넘겨 주면 된다.

<br>

```python
type(classname, superclasses, attributes_dict)
```

<br>

Django를 사용하다보면 모델을 admin에 일일이 등록해줘야 하는 불편함이 있다. 

이럴 경우 type을 이용하면 단순 admin에 등록이 필요한 모델의 경우 손쉽게 추가해줄 수 있다.

<br>

전체 예제 코드

```python

# coding: utf-8
from django.contrib import admin
from django.contrib.admin.sites import AlreadyRegistered


 def get_list_display(model):
    return [field.attname for field in model._meta.fields]


 def get_raw_id_fields(model):
    return [field.name for field in model._meta.fields if field.remote_field]


 def auto_register(model):
    list_display = get_list_display(model)
    raw_id_fields = get_raw_id_fields(model)

    admin_class_name = f'{model._meta.object_name}Admin' 
    admin_model = type(admin_class_name, (admin.ModelAdmin, ), {
        'list_display': list_display, 
        'raw_id_fields': raw_id_fields})

    try:
        admin.site.register(model, admin_model)
    except AlreadyRegistered:
        pass 
```

<br>

list_display에 전체 필드를 추가하고, ForeignKey의 경우에는 raw_id_fields에 추가한 다음 admin 모델을 만들고 admin 페이지에 등록하는 함수이다.   

type을 살펴보면 각 model의 이름에 Admin이라는 suffix를 붙인 이름을 첫 인자로 받으며,
admin클래스가 되기 위해 상속 받을 모델인 admin.ModelAdmin을 두 번째 인자로 받는다. 
마지막으로 각 admin 클래스의 list_display와 raw_id_fields가 될 값들을 인자로 받는다. 


<br>

위의 코드를 실제로 사용할 때는 auto_register에 model 클래스를 넘겨주기만 하면 된다.

```python

models = [
    'Blog',
    'Post',
    'Comment',
]

for model in models:
    auto_register(model)

```

그러면 BlogAdmin, PostAdmin, CommentAdmin이 자동으로 생성되어 admin에 등록된다.




<br>
<br>

