---
layout: post
title: "MySQL Transaction isolation level"
date: 2019-04-26 10:25:25 +0900
categories: [backend]
encoding: UTF-8
---

<br>
<br>

### MySQL 트랜잭션 격리 수준

<br>

트랜잭션 격리 수준이란, 트랜잭션이 다른 트랜잭션의 수정 및 조회 중인 데이터를 언제보여 주느냐를 결정하는 것이다. 

즉, 격리 수준이 A 일때 트랜잭션 a에서는 트랜잭션 b에서의 변경 내용을 볼 수 없지만, 격리 수준이 B일 때는 트랜잭션 b에서 일어나는 모든 일을 트랜잭션 a에서 볼 수 있다.

<br>

#### 데이터 부정합

<br>

격리 수준에 따라 발생할 수 있는 데이터 부정합 현상은 3 가지이다. 

* DIRTY READ
    * 다른 트랜잭션에서 처리한 작업이 완료되지 않았는데도 다른 트랜잭션에서 볼 수 있게 되는 현상을 DIRTY READ 라고 한다. 
* NON-REPEATABLE READ
    * 하나의 트랜잭션 내에서 똑같은 SELECT 쿼리를 실행했을 때 항상 같은 결과를 가져와야 한다는 REPEATABLE READ 정합성에 어긋난다.
    * A 트랜잭션에서 count라는 colum의 값을 1000 -> 5000으로 변경한 후 COMMIT하면 다른 트랜잭션 B에서는 값이 1000이었다가 5000이 된다.
* PHANTOM READ
    * 하나의 트랜잭션에서 어떤 범위를 두 번 이상 조회할 때, 이전 쿼리에는 없던 레코드가 나타나는 현상을 말한다.



<br>

#### 격리 수준

<br>

아래로 내려갈 수록 더 강한 격리 수준이다.


* READ UNCOMMITTED
    * COMMIT이나 ROLLBACK 여부에 상관 없이 다른 트랜잭션에서 보여진다. 
    * DIRTY READ가 발생할 수 있다. 
    * RDBMS 표준에서는 트랜잭션의 격리 수준으로 인정하지 않을 정도로 정합성에 문제가 많은 격리 수준이다. 
* READ COMMITTED
    * 오라클 DBMS에서 기본적으로 사용하는 격리 수준이며, 온라인 서비스에서 가장 많이 선택되는 격리 수준이다. 
    * COMMIT이 완료된 데이터만 다른 트랜잭션에서 조회할 수 있다. 
    * NON-REPEATABLE READ가 발생할 수 있다. 
* REPEATABLE READ
    * InnoDB 스토리지 엔진에서 기본적으로 사용되는 격리 수준이다.
    * 바이너리 로그를 가진 MySQL 장비에서는 최소 REPEATABLE READ 격리 수준 이상을 사용해야 한다. 
    * MVCC 방식이 사용된다. 
* SERIALIZABLE
    * 가장 엄격한 격리 수준으로, 동시 처리 성능도 다른 트랜잭션 격리 수준보다 떨어진다. 
    * 읽기에도 공유 잠금이 생긴다. 
    * 한 레코드 당 한 트랜잭션의 접근만을 허용한다. 





<br>
<br>