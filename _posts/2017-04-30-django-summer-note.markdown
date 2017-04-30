---
layout: post
title: "Django-summernote"
date: 2017-04-30 17:31:32 +0900
categories: [django]
encoding: UTF-8
---

웹 어플리케이션 개발 시 게시판용 에디터를 자체적으로 구현하기에는 어려운 점이 많다.
그 때 할용하기 좋은 것이 summernote라는 오픈소스 WYSIWYG(What You See Is What You Get)
에디터이다. 

거기에 django 프로젝트를 위해 존재하는 django-summernote는 적용하기 매우 쉽다. 

<br/>

[저장소: summernote/django-summernote](https://github.com/summernote/django-summernote)


<br/>

#### 설치 

<br/>

설치도 pip를 통해 간단하게 할 수 있다.


```shell
pip install django-summernote
```

<br/>

#### 설정

프로젝트 settings.py 안에 INSTALLED_APPS에 django_summernote를 추가해주면 된다.

<br/>

{% highlight python %}

# settings.py

[....]


INSTALLED_APPS = (
    'django.contrib.admin',
    'django.contrib.auth',
    [...]
    'django_summernote', # 추가
)


[....]

{% endhighlight %}

<br/>

추가적으로 summernote의 설정을 바꾸고 싶다면, settings.py에 

SUMMERNOTE_CONFIG = {} 를 선언하여 옵션을 수정하면 된다. 

옵션은 저장소 README.md에 자세히 나와있다.


<br/>

{% highlight python %}

# urls.py

[....]

urlpatterns = patterns('',
    [...]
    (r'^summernote/', include('django_summernote.urls')), # 추가
    [...]
)


[....]

{% endhighlight %}

<br/>

그리고 이미지 파일 등을 처리하기위해 MEDIA_URL등의 설정이 추가적으로 필요하다. 


<br/>

그런 다음 마이그레이트를 실행해주면 된다.

```shell
python manage.py migrate
```

<br/>

#### 사용

사용법도 간단하다. 사용하고자 하는 필드(content가 들어갈 필드)에 widget을 SummernoteWidget이나 
SummernoteInplaceWidget으로 지정해주면 된다. 

<br/>

{% highlight python %}

# forms.py

[....]

from django_summernote.widgets import SummernoteWidget
# 또는 from django_summernote.widgets import SummernoteInplaceWidget

class BasicForm(forms.Form):
    content = forms.CharField(widget=SummernoteWidget()) # iframe으로 적용
    # 또는 content = forms.CharField(widget=SummernoteInplaceWidget()) 

{% endhighlight %}

<br/>

템플릿 단에서 해당 필드를 풀력한 때 safe 필터를 적용해야한다.


content|safe



<br/>
<br/>

admin단에서 적용하고 싶다면, 

<br/>

{% highlight python %}

# admin.py

[....]

from django_summernote.admin import SummernoteModelAdmin
from .models import BasicModel

class BasicModelAdmin(SummernoteModelAdmin):
    [...]


admin.site.register(BasicModel, BasicModelAdmin)

{% endhighlight %}


<br/>
<br/>

