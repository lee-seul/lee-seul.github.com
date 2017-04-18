---
layout: post
title: "Data Structure, 05, Binary Search Tree(BST) "
date: 2017-04-09 17:31:22 +0900
categories: [algorithm, python]
encoding: UTF-8
---

자료구조란 데이터를 효율적으로 이용할 수 있도록 저장하는 방법이며,
이를 통해 효율적인 알고리즘을 구현할 수 있다. 

자료구조는 선형구조와 비선형구조로 구분된다. 

<br/>


### 5. 이진 탐색 트리(Binary Search Tree)

이진 탐색 트리는 이진 트리로서 공백일 수 있고 공백이 아니라면 다음 4가지 성질을 만족해야한다. 

1. 모든 원소는 키를 가지며, 어떤 두 원소도 동일한 키를 갖지 않는다.(가지도록 확장할 수는 있음)
2. 왼쪽 서브트리의 키 값들은 그 루트의 키값보다 항상 작다.
3. 오른쪽 서브트리의 키 값들은 그 루트의 키값보다 항상 크다.
4. 왼쪽과 오른쪽 서브트리도 모두 이진 탐색 트리이다.  

<br/>


![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/BST01.png)

<br/>

이진 탐색 트리는 위 그림처럼 왼쪽 서브트리는 그 루트(5)보다 모두 작고, 오른쪽 서브트리는 루트보다 모두 크다. 



<br/>
<br/>

#### 5.1 BST 탐색 

<br/>

이진 탐색 트리는 이름에서 알 수 있듯이 이진 탐색을 하기에 매우 좋은 구조이다.

탐색의 방법은 가장 먼저, 찾으려는 값을 루트와 비교한 후 작으면 왼쪽 서브트리로 크면 오른쪽 서브트리로 진행하면 된다.

<br/>

{% highlight python %}


 class BinaryTree:



 
{% endhighlight %}




<br/>
<br/>






