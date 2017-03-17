---
layout: post
title: "Test Coverage"
date: 2017-02-05 00:23:31 +0900
categories: [test]
encoding: UTF-8
---

Test Coverage란 테스트가 어느 정도 수준으로 이루어지가를 나타내는 지표 중 하나이다. 보통 %로 표현하며,
소스 코드에 대하여 몇 %만큼 커버되는지를 나타낸다. 얼마만큼 커버되는 지에 대한 지표이므로 실제로 테스트가
적합한지를 평가하는데 적합하지 않을 수 있다. Test Coverage라는 수치에만 집중하여 의미 없이 Coverage만
높은 테스트 코드를 작성한다면, 의미있는 테스트가 될 수 없다.
<br/>
<br/>


### 1. **Statement Coverage**

Line Coverage라고도 하는데, 소스 코드의 라인 중 테스트 코드로 테스트되는 라인이
얼만큼 되는지를 측정한다. 200줄에 코드가 있고 테스트 코드가 140줄에 코드에 대해 테스트를
수행한다면 Statement Coverage는 70%이다. 

Python에서는 Coverage.py라는 오픈소스를 이용하여 간단하게 Statement Coverage를
측정할 수 있다.

[https://coverage.readthedocs.io/en/coverage-4.3.4/](https://coverage.readthedocs.io/en/coverage-4.3.4/)

<br/>


### 2. **Branch(Decision) Coverage**

프로그램의 Branch(분기)들을 얼마만큼 커버하는지를 말한다. 예를 들어 아래에 소스에서
입력 값 (0, 10, 20, 30)의 테스트 데이터를 입력할 경우 A, B, C, D 모두 테스트하기때문에 Test Coverage는 100%이다.
만약 테스트 데이터가 (0, 9, 11, 20)이라면 D조건을 테스트하지 못하므로 Test Coverage는 75%가 된다.

```python
n = int(input())

if 0 <= n < 10: # A 조건
    print("A")
elif 10 <= n < 20 : # B 조건
    print("B")
elif 20 <= n < 30: # C 조건
    print("C")
else:             # D 조건
    print("D") 
```

Branch Coverage 또한 Statement Coverage에서 언급한 Coverage.py에 --branch를 통해
측정이 가능하다. 

<br/>

### 3. **Condition Coverage**

소스 코드 내에 사용된 개별 조건을 얼마만큼 커버하는 지를 나타낸다. 아래의 코드와 같은 경우
a > 10, b > 10이라는 2가지 condition에 각각 True/False가 수행되는 지를 기준으로 한다.

예를 들어 a = 11(True), b = 9(False)와 a = 10(False), b = 11(True)라는 
두 가지 테스트 데이터로 테스트를 수행할 경우 각각의 condition a > 10, b > 10에 True, False가
모두 확인되므로 Condition Coverage는 100%이다. (이 경우 Branch Coverage 50%, 두 경우 모두 False이므로
True에 대해서는 테스트하지 못함)

만약 a = 10(False), b = 10(False)라는 테스트 데이터 한 가지만으로 테스트할 경우
각각 condition에 False값만 테스트가 가능하기때문에 Condition Coverage는 50%이다.

```python
a, b = map(int, input().split()) # a, b에 int값을 입력으로 받음

if a > 10 and b > 10: # a > 10, b > 10이라는 condition이 2개
    print(a + b)
```

<br/>

### 4. **Condition/Decision Coverage(C/DC)**

Condition/Decision Coverage는 이름 그대로 Condition Coverage와 Decision Coverage, 두 가지를
복합적으로 측정하는 Test Coverage이다. 

조건문의 True/False, Condition의 True/False를 얼마만큼 커버하는지 나타낸다. 예를 들어
아래의 코드와 같이 하나의 조건문에 2개의 개별 조건이 들어있는 상황에서 
A, B와 A or B 3 가지 모두에 True/False를 테스트하여야한다. 

(A = True, B = True)->True, (A = False, B = False)->False의 2 가지
테스트 데이터를 입력할 경우 A, B 각 개별조건에 대해서도 True/False를 모두 확인할 수 있고
A or B라는 조건 또한 True/False를 테스트할 수 있으므로 Coverage는 100%가 된다.

```python
if A or B: 
    print("Hello")
```

<br/>

### 5. **Modified Condition/Decision Coverage(MC/DC)**

Modified Condition/Decision Coverage는 Condition/Decision Coverage를 조금 더
발전시켜 모든 condition이 독립적으로 Branch의 True/False에 영향을 주어야만 적절한
테스트 데이터로 인정받을 수 있다. 

아래의 소스에서 Condition/Decision Coverage의 경우 (A = True, B = True),
(A = False, B = False)라는 2 가지 테스트 데이터만으로도 충분하였으나 
이 경우 B에 True값이 입력되었을 때 독립적으로 Branch에 영향을 주지 못하므로 
Modified Condition/Decision Coverage눈 66.67%가 된다. 

아래의 경우에서 100%의 coverage의 테스트를 작성하기위해서는 (A = True, B = False)
, (A = False, B = True), (A = False, B = False)라는 3가지 테스트 데이터가
필요하다. 

(A = True, B = False) 경우 A가 Branch를 True,<br/>
(A = False, B = True) B가 Branch를 True,<br/>
(A = False, B = False) A, B 모두가 False여야 Branch가 False가 되므로 모든 경우를
테스트할 수 있다.

```python
if A or B: 
    print("Hello")
```



<br/>
<br/>



