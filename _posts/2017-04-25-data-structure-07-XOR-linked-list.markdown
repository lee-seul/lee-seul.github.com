---
layout: post
title: "Data Structure, 07, XOR Linked List "
date: 2017-04-25 20:51:32 +0900
categories: [algorithm]
encoding: UTF-8
---

자료구조란 데이터를 효율적으로 이용할 수 있도록 저장하는 방법이며,
이를 통해 효율적인 알고리즘을 구현할 수 있다. 

자료구조는 선형구조와 비선형구조로 구분된다. 

<br/>


### 7. 메모리 효율적인 이중 연결 리스트 

<br/>
<br/>


일반적으로 이중 연결 리스트는 현재 노드에서 이전 노드와 다음 노드를 가르키는 2개의 포인터를 가진다. 
하지만 2개의 포인터로 인해 추가적인 메모리가 필요하다. 이를 해결하기위해 포인터 1개만을 활용하여 
이전 노드와 다음 노드의 주소값을 얻을 수 있는 자료구조를 메모리 효울적인 이중 연결 리스트 혹은 XOR Doubly Linked List라고 한다. 


<br/>

메모리 효울적인 이중 연결 리스트는 데이터와 하나의 포인터를 갖는데 포인터에는 이전 노드를 가르키는 포인터와 다음 노드를 가르키는 포인터의 배타적 논리합(XOR)의 결과를
가지고 있다.

<br/>

ptr = 이전 노드 포인터 ^ 다음 노드 포인터 

<br/>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/XOR.png)

<br/>


위의 그림에서 B에서 이전 노드를 구하려면 B.ptr ^ C를 수행하면 된다. 
B.ptr은 A ^ C 이므로 B.ptr ^ C는 (A ^ C) ^ C와 같다. C ^ C는 0이므로 A의 값을 알 수 있다. 


마찬가지로 B애서 다음 노드를 구하려면 B.ptr ^ A를 수행하면 된다.
B.ptr은 A ^ C, B.ptr ^ A 는 (A ^ C) ^ A은 C ^ (A ^ A)와 같으므로 C의 값을 알 수 있다.   


<br/>
<br/>

