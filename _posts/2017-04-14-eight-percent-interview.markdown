---
layout: post
title: "Interview Question, 8 Percent  "
date: 2017-04-14 19:33:22 +0900
categories: [python, others]
encoding: UTF-8
---

작년 하반기쯤 발견했던 글이지만, 8 퍼센트에 입사 지원을 한 이후에 다시 생각나서 
문제 풀이에 재도전.. 

당시에는 접근 방법조차 생각나지 않았었다. 

[에잇퍼센트 개발 면접 문제를 풀어보자](https://brunch.co.kr/@sunghokimnxag/5)


<br/>


##### 문제 쪼개기 

일단, For Loop, Array, String, Assignment를 사용하지 못하는 제한 사항이 있다.

(뭔가 상태값을 가지지 않고 데이터 흐름만으로 처리하는게 함수형 프로그래밍같은데 
함수형 프로그래밍을 잘모른다.) 


입력 값에 대한 제한이 없는 걸로 보아 일단 최대한 큰 값을 고려한다. 


제한 사항만 없다면, 문제 자체는 간단하다. 7의 배수이거나, 7이 포함된 숫자일 경우 
증가하는 값에 곱하기 -1을 해주면 된다. 


이 때 확인해야하는 변수가, 

1. 입력값 x
2. 변화할 값 n
3. n이 변화한 횟수를 카운트할 c
4. 증가값(+1, -1)이 저장될 plus


그리고 쪼개진 작은 문제들 

1. x == c(진행 횟수)
2. n + plus
3. c % 7 == 0 
4. '7' in str(c) 


문제 예시에 파라미터로 x만을 받으므로 일단, pingpong 함수부터 구현한다.


<br/>


{% highlight python %}


def pingpong(x):
    return get_pingpong(x, n, c, plus) # 실제 결과 값을 계산할 함수
    

print(pingpong(int(input()))) # x를 입력받아 결과 값을 출력

{% endhighlight %}


<br/>


분해한 문제 4개를 하나씩 풀어보면,

##### 1. x == c(진행 횟수)

<br/>

{% highlight python %}


# pingpong(x)에서 호출했던, get_pingpong
def get_pingpong(x, n, c, plus):
    if x == c:
        return n


{% endhighlight %}

<br/>


##### 2. n + plus

<br/>

{% highlight python %}


def get_pingpong(x, n, c, plus):
    if x == c: # 재귀 종료문
        return n
    # c가 x가 될 때까지 실행될 재귀
    return get_pingpong(x, n+plus, c+1, plus)

{% endhighlight %}

<br/>

재귀가 거듭될수록 c가 1씩 증가하여, x가 되면 n을 리턴하고 모든 재귀가 종료된다. 


이제 c가 7의 배수이거나 7을 포함하는지 검사하여 True일 경우 plus에 -1을 곱해주면 된다. 

일단, 

##### 3. c % 7 == 0 부터 

<br/>

{% highlight python %}

def seven(c, plus): # c가 7의 배수이면, plus에 -1을 곱한 값을 7의 배수가 아니면 plus를 리턴
    if not c % 7:
        return plus * (-1)
    return plus


def get_pingpong(x, n, c, plus):
    if x == c: # 재귀 종료문
        return n
    # c가 x가 될 때까지 실행될 재귀
    return get_pingpong(x, n+seven(c, plus), c+1, seven(c, plus))

{% endhighlight %}

<br/>

seven(c)를 통해 7의 배수일 때 증가값의 변화를 주었다. 마지막으로 

##### 4. '7' in str(c) 

문제에서 String을 쓰지말라고 했고, 입력 값에 제한이 없다는 전제하에 7이 포함된 수인지를 검사하기위해

7을 빼서 10을 나눈 값의 나머지가 0이라면, 1의 자리가 7이라는 점을 이용한다.

<br/>

{% highlight python %}

def having_seven(c):
    if not c: # False일 때 재귀를 탈출
        return False
    
    if not (c-7) % 10: # True일 때 재귀를 탈출
        return True
    return having_seven(c//10) # 1, 10, 100... 에 7이 포함되었는지를 계속해서 검사


def seven(c, plus): 
    if not c % 7 or having_seven(c): # 7의 배수 혹은 7이 포함되었는지를 함께 검사
        return plus * (-1)
    return plus

{% endhighlight %}

<br/>


최종적으로 코드를 합치면, 

<br/>

{% highlight python %}

def having_seven(c):
    if not c:
        return False

    if not (c-7) % 10:
        return True
    return having_seven(c//10)


def seven(c, plus):
    if not c % 7 or having_seven(c):
        return plus * (-1)
    return plus


def get_pingpong(x, n, c, plus):
    if x == c:
        return n
    return get_pingpong(x, n+seven(c, plus), c+1, seven(c, plus))


def pingpong(x):
    return get_pingpong(x, 1, 1, 1) # 초기 값을 설정해준다.


print(pingpong(int(input())))


{% endhighlight %}

<br/>

1000이상의 값을 입력했더니 RecursionError: maximum recursion depth exceeded를
내뿜길래 뭔가 했더니 파이썬에서는 리컬전을 1000으로 제한되어있다고 한다.

995까지만 해결이 가능하다....

<br/>
<br/>
