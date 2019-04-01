---
layout: post
title: "Python Mac OS 한글 자모 분리 이슈"
date: 2019-04-01 10:43:01 +0900
categories: [python]
encoding: UTF-8
---

<br>
<br>

### Python Mac OS 한글 자모 분리 이슈

<br>


Mac OS에서 python으로 한글을 다루다 보면 아래와 같은 문제를 겪을 수 있다. 
(영원히 안 겪을 수도 있다)
```python
>>> string = '파이썬'  
>>> print(string[0])
ㅍ
```

이런 현상이 나타나는 이유는 Mac OS에서 유니코드를 처리하는 방식 때문이다. 

Mac OS에서는 유니코드를 NFD(Normalization Form Decomposition)라는 방식을 사용해서 저장하고 보여주는데, 이 방식은 자모를 분리하여 저장한 후 보여줄 때 합쳐서 보여주는 방식이기 때문이다. 

즉, 파이썬이라는 단어가 있을 때 `ㅍㅏㅇㅣㅆㅓㄴ` 이라고 저장한 후 보여줄 때만 합쳐서 `파이썬` 이라고 보여준다는 뜻이다. 

<br>

문제 해결은 매우 간단하다. NFD 형식을 NFC(Normalization Form Composition) 파태로 바꾸면 끝난다. 

Python에서는 아래와 같이 처리할 수 있다.


```python
>>> import unicodedata
>>> string = '파이썬'
>>> string = unicodedata.normalize('NFC', string)
>>> print(string[0])
파
```


<br>
<br>

