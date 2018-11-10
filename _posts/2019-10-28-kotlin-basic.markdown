---
layout: post
title: "Kotlin basic"
date: 2018-11-10 21:11:23 +0900
categories: [other]
encoding: UTF-8
---

<br>
<br>

## 코틀린 기본 문법

<br>

### 변수와 상수 
```kotlin
var a: Int = 1
val b: Int = 2
```

변수는 var, 상수는 val로 선언

<br>

### 함수
```kotlin
fun hello(string: String): Unit {
    println(string)
}
```
반환 값이 Unit인 경우 생략 가능

<br>

### 컬렉션

#### 배열
```kotlin
val array: Array<Int> = arrayOf(1, 2, 3)
val array = arrayOf(1, 2, 3)
array[0] = 3 // [3, 2, 3] 
```

<br>

#### 리스트 
```kotlin
val list: List<Int> = listOf(1, 2, 3)
val list = listOf(1, 2, 3)
```

<br>

#### 변경 가능한 리스트
```kotlin
val list = mutableListOf(1, 2, 3)

list.add(4)
list.removeAt(0)
```

<br>

#### 맵
```kotlin
val map = mapOf("a" to 1, "b" to 2, "c" to 3)
```

<br>

#### 변경 가능 맵
```kotlin
val map = mutableMapOf("a" to 1, "b" to 2, "c" to 3)
map["a"] = 4
```

<br>

#### 집합
```kotlin
val set = setOf(1, 2 ,3)
```

<br>

#### 수정 가능한 집합
```kotlin
val set = mutableSetOf(1, 2, 3)
set.add(4)
set.remove(1)
```

<br>


### 제어문


#### if

```kotlin
var a = 5
var b = 0

if (a == 2) {
    b = 1    
} else {
    b = 2
}
```
<br>

#### for
```kotlin
val array = arrayOf(1, 2, 3, 4)

for (num in array) {
    println(num)
}

```

<br>

#### while
```kotlin
var a = 0
while (x < 10) {
    println(x)
    x++
}
```


<br>


### 클래스 


#### 선언
```kotlin
class God {

}

val god = God()
```

<br>

#### 생성자
```kotlin
class God {
    constructor(power: String) {
        println(power)
    }
}
```

<br>

#### 프로퍼티 
```kotlin
val god = God("슈퍼 파워")
god.power = "?"
pringln(god.power)

```

코틀린에서는 게터, 세터없이 프로퍼티를 이용해서 접근

<br>

#### 접근 제한자
- public
- private
- protected
- internal(같은 모듈 내에서만)

<br>

#### 상속
```kotlin
open class God {

}

class DemiGod: God() {

}
```

open 키워드가 없으면 상속이 불가능

<br>

#### 내부 클래스
```kotlin
class Outer {
    var a = 1

    inner class Inner {
        a = 10
    }
}

```
inner 라는 키워드를 사용해야 Outer 클래스의 변수에 접근이 가능하다. 

<br>

#### 추상 클래스 
```kotlin
abstract class Abs {
    abstract fun a()
}
```

<br>


### 인터페이스 

#### 선언 
```kotlin
interface Clickable {
    fun click()
}
```

<br>

#### 구현
```kotlin
class Button: Clickable {
    override fun click() {
        println("클릭")
    }
}
```

<br>

### 기타

#### ?
```kotlin
val a: String? = null
```
코틀린은 기본적으로 null을 허용하지 않지만 ?를 사용하면 null을 허용하도록 할 수 있다. 

<br>


#### !!
변수 뒤에 !! 를 추가하면 null값이 아님을 보증한다. 

<br>



#### lateinit
```kotlin
lateinit var a: String

a = "초기화"
```

<br>




<br>
<br>