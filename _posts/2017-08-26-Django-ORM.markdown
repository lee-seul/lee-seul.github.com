---
layout: post
title: "Django ORM "
date: 2017-08-26 23:26:32 +0900
categories: [django]
encoding: UTF-8
---

ORM
- Object Relation Mapper
- Django model(객체)을 DB table로 변환시켜 조작하게 해줌(SQL없이도 DB를 조작할 수있음)
- DBMS에 상관없이 사용 가능하고 객체지향의 관점에서 DB를 설계할 수 있음
- 단, 기계가 작성하는 것이기때문에 반드시 효율적인 SQL로 변환되는 것은 아님
 
<br/>


### django ORM 기본 사용법

<br/>
<br/>


{% highlight python %}

class Post(models.Model):
    """블로그 포스트
    """
    title = models.CharField(max_length=100)
    content = models.TextField()
    like = models.IntegerField(default=0)
    created_at = models.DateTimeField(auto_now_add=True)


class Comment(models.Model):
    """포스트의 댓글
    """
    post = ForeignKey(Post, on_delete=models.CASCADE)
    content = models.CharField(max_length=255)
    created_at = models.DateTimeField(auto_now_add=True)


{% endhighlight %}


간단한 모델 클래스 2개가 있다. 2개의 클래스로 기본적인 사용법을 예로 들면, 

<br/>

#### Select

<br/>

{% highlight python %}

Post.objects.all() # Post 테이블을 모두 호출, 쿼리셋을 반환

post = Post.objects.get(id=1) # id값이 1인 Post 오브젝트를 반환

Comment.objects.filter(post=post) # id가 1인 Post에 달린 댓글들의 쿼리셋을 반환

Comment.objects.filter(post=post).order_by('-created_at') # 생성 시점으로 정렬

{% endhighlight %}

위는 ORM을 사용한 select명령들이다. get이나 filter에서 사용된 조건은 where절과 동일하다고 생각하면 쉽다.
위 4개의 명령 중 get은 Post의 오브젝트를 바로 반환하지만, filter와 all의 경우 쿼리셋을 반환하는 차이가 있다. 
django에서는 쿼리가 평가(실행)되는 시점이 실제 쿼리셋이 사용될 때까지 지연된다. 그 때문에 
filter와 all은 실제로는 DB 커넥션이 이루어지지 않고 쿼리셋만을 반환한다. 

<br/>

order_by의 경우 특정 칼럼을 기준으로 정렬할 때 사용한다. -의 경우 내림차순, 기본은 오름차순이다.

만약, Model class에 Meta클래스를 선언하고 ordering을 줄 경우 해당 Model class의 쿼리셋의 기본 정렬순서를 정해줄 수 있다.


{% highlight python %}

Comment.objects.exclude(post=post) # 해당값 제외한 쿼리

{% endhighlight %}



<br/>

추가로 다양한 조건으로 쿼리를 실행할 수 있다.


{% highlight python %}

import datetime

today = datetime.date.today() # 2017-08-26


Post.objects.filtert(created_at__lt=today) # 오늘보다 과거에 작성된 Post
# lt = less than(<) / lte = less than equal(<=)
# gt = greater than(>) / gte = greater than equal(>=)

Comment.objects.filter(created_at__day=today.day) # 연,월에 상관없이 26일에 작성된 댓글
# year, month, day 모두 사용 가능

id_list = [1, 3, 5, 6]
Comment.objects.filter(id__in=id_list) # id값이 id_list에 포함된 경우

Comment.objects.filter(content__isnull=False) # title이 null이 아닌 경우

Post.objects.filter(title__contain='django') # title에 django라는 단어를 포함하는 경우
Post.objects.filter(title__startswith='django') # title이 django로 시작하는 Post
Post.objects.filter(title_endswith='django') # title이 django로 끝나는 Post
# contain, startswith, endswith는 대소문자를 구분하여 검색
# 앞에 i를 붙인 icontain, istartswith, iendswith는 대소문자 구분없이 검색

last_week = today - datetime.timedelta(days=7) # 일주일 전 날짜를 반환

Comment.objects.filter(created_at__range=[last_week, today]) # 지난주부터 오늘 전까지 작성된 Comment

# postgreSQL을 사용한다면 search도 사용 가능
Comment.objects.filter(search='django') # django라는 단어가 포함된 모든 Comment, Full text search라 훨씬 빠름

{% endhighlight %}

<br/>

#### Insert

<br/>

{% highlight python %}

post = Post(title='django test') # django test라는 title을 가진 Post
post.save() # save까지 해주어야 실제로 생성

Post.objects.create(title='django test') # 위의 두 명령어를 모두 실행한 것과 같은 결과

{% endhighlight %}


<br/>

#### Update

<br/>

{% highlight python %}

post = Post.objects.get(id=1)
post.title = 'update post title' # title을 변경, DB 반영 X
post.save()  # DB 반영됨

# 다중행의 update
Post.objets.all().update(title=u'실제로는 사용될리 없는 쿼리') # 모든 Post의 title을 변경, filter를 이용할 수도 있음

{% endhighlight %}


<br/>

#### Delete

<br/>

{% highlight python %}

post = Post.objects.get(id=1)
post.delete() # Post 객체 삭제, DB 반영

# 다중행의 delete
Post.objets.all().delete() # 모든 Post 제거

{% endhighlight %}


<br/>
<br/>

### django ORM 효율적 사용

<br/>

앞서 말했듯이 django에서 ORM을 통해 얻은 쿼리의 실제 실행 시점은 유저가 쿼리를 실행한 시점이 아닌 경우가 많다.
if나 for문, 혹은 실제로 데이터를 가지고 와야할 때 실행되는데 일반적으로는 성능 이슈에서 큰 부분을 차지하는 DB connection이 
무분별하게 생성되는 것을 막아준다. 하지만 대부분의 쿼리가 지연되기때문에 몇가지 문제가 발생할 수 있는데 
그에 대한 해결 방법을 설명하고자 한다.

<br/>

#### 쿼리셋의 캐시

<br/>

쿼리셋은 한번 평가(실행)되면 캐시되는데 쿼리셋을 캐시로 생성할지 말지를 경우에 따라 고려해주어야한다.
같은 쿼리가 여러번 실행되어햐 할 경우에는 있는 그대로 쿼리를 실행하면, django ORM의 캐시 혜택을 받을 수 있지만,
쿼리가 한번만 실행되는 경우나, 캐시되어야할 쿼리셋이 너무 큰 경우에는 캐시가 생설될 경우 오히려 비효율적이다.

특히, 쿼리셋에 값이 있는지 여부나 쿼리셋의 길이만을 알고 싶은 경우 exists()와 count()를 활용해야한다.


{% highlight python %}

posts = Post.objects.all() 

# 이러면 안된다
length = len(posts) # 쿼리셋에 포함된 오브젝트 숫자만을 위해 DB에서 모든 행을 가져와 캐시시킨다. 

# 대신
length = posts.count() # 행의 숫자만을 반환


comments = Comment.objects.filter(created_at__year=2017) # 2017년에 작성된 댓글 

# 안티 패턴
if comments: # Comment를 모두 호출~
    print '이러면...'


if comments.exists():
    print 'Good'

# list()... 
posts = list(posts) # DB에 저장된 데이터가 클 경우 굉장한걸 보게 될 수도.. 

# 순회해야할 경우는 iterator()를 
for post in posts.iterator():
    print post.title

{% endhighlight %}



<br/>

#### 조금 더 효율적인 쿼리

<br/>


ORM을 사용하다보면 흔히 생기는 성능 이슈가 N+1 문제인데, django에서는 모든 경우 적용되는 것은 아니지만 
select_related와 prefetch_related를 활용하여 해결할 수 있다.

##### N+1 문제

모든 Post의 모든 Comment를 불러낼 때 일어날 수 있는 문제이다. 


{% highlight python %}

posts = Post.objects.all() # 1

for post in posts: # n
    # comment = post.comment_set.all() 혹은 
    comment = Comment.objects.filter(post=post) # 예시일뿐

# 각 post의 comment를 불러오기위해 쿼리는 n+1번 발생한다.


{% endhighlight %}


위의 경우 각 post의 comment를 가져오기위해 쿼리가 N+1번 발생하게되고, 불필요한 DB 커넥션이 생성되어 성능에 문제가 
생길 수 있다.(모든 쿼리를 저런식으로 작성한다면..)

이때 1:N의 관계일 때 사용할 수 있는 것이 select_related, N:M일 때는 prefetch_related이다.
두 메소드는 ForeignKey와 ManyToManyField로 선언된 경우만 사용 가능, 즉 Post에서 select_related('comment')만 가능하다.



{% highlight python %}

posts = Post.objects.all().select_related('comment') 

for post in posts: # n
    comment = Comment.objects.filter(post=post) # 이미 캐시되어 실행안됨

{% endhighlight %}


<br/>

##### values와 values_list

<br/>

values와 values_list는 Model class에서 특정 필드만을 필요로할 때 사용할 수 있으며, 클래스 전체를 호출하는 것 보다 효율적이다.

<br/>

{% highlight python %}

Post.objects.all().values('title', content) # {'title':  value, 'content': value} 로 이루어진 리스트 반환

Post.objects.all().values_list('title', 'content') # (title, content)와 같이 값으로 이루어진 튜플의 리스트를 반환

Post.objects.all().values_list('title', flat=True) # 해당 필드값으로 이루어진 리스트를 반환

{% endhighlight %}

<br/>

#### 고급 쿼리

<br/>

django 쿼리셋을 조금 더 효과적으로 쓸 수 있게해주는 Annotation과 Aggregation에 대해 소개하고자 한다.
두 메소드는 django.db.models에 있는 F,Sum, Count나 
djaogn.db.models.functions에 있는 Length 등과 자주 쓰인다.

Annotation은 쿼리에 임시 쿼리를 추가하는 메소드이다.


{% highlight python %}

from django.db.models import Count
from django.db.models.functions import Length


Post.objects.filter(comment__isnull=False).annotate(count_comment=Count('comment'))
# 각 Post의 comment 개수를 값으로 가지는 count_comment라는 이름의 필드를 반환

Post.objects.annotate(title_len=Length('title')).filter(title_len__lte=100) 
# title의 길이를 값으로 가지는 title_len이라는 임시 필드를 만들고 그 값을 통해 필터링

{% endhighlight %}


<br/>

Annotation이 칼럼을 늘린다면, Aggregation은 반대로 Sum등을 활용하여 여러행을 하나로 합쳐서 하나의 행으로 만들어준다.


{% highlight python %}

from django.db.models import Sum


Post.objects.filter(comment__isnull=False).aggregate(totle_like=Sum('like'))
# 위의 쿼리는 1개의 행만을 리턴한다. (comment를 가진 Post의 like를 모두 더한 값)


{% endhighlight %}

