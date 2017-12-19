---
layout: post
title: "MySQL, MyISAM vs InnoDB"
date: 2017-12-19 23:50:20 +0900
categories: [others]
encoding: UTF-8
---

<br/>
스토리지 엔진이란 데이터베이스에 데이터를 insert, update, delete, select 등을 수행할 때 필요한 컴포넌트이다. <br/>
MySQL에는 MyISAM, InnoDB, NDB, Archive, Fedorated, Memory, Partner, Community, Custom 등등의 <br/>
다양한 스토리지 엔진이 존재한다. 그 중 현재 가장 많이 쓰이고 있는 MyISAM과 InnoDB에 대해 정리해보고자 한다.<br/>
 <br/>



 ### MyISAM

 <br/>

MyISAM은 MySQL 5.5 이전에는 기본 스토리지 엔진이었지만, 5.5 이후부터는 InnoDB로 기본 스토리지 엔진이 변경되었다. <br/>
InnoDB와 대비되는 MyISAM의 가장 큰 특징은 트랜잭션을 지원하지 않는다는 점이며, 기능적으로 InnoDB에 비해 <br/>
매우 단순하다. 앞서 말했듯이 트랜잭션지원하지 않으며, FK도 사용이 불가능하여 데이터 무결성이 보장되지 않는다. <br/>
또한 Table-level lock을 기본적으로 사용하기 때문에 insert, update 속도가 매우 느리다. <br/>


대신 MyISAM은 읽기 속도가 매우 빠르고 Full-text 인덱싱이 가능하다. 때문에 데이터의 변경이 거의 없는 경우를 제외하고는 사용하기에 어려운 점이 많다.



<br/>
<br/>

 ### InnoDB

 <br/>
InnoDB는 ACID(원자성, 일관성, 고립성, 지속성) 트랜잭션을 지원한다(commit, rollback 등이 가능). <br/>
 또한 FK를 지원하며 백업 및 특정 시점 복구가 가능하다. <br/>

Row-level lock을 사용하기때문에 데이터 변경 작업 속도가 MyISAM에 비해 빠르다. <br/>
읽기, 쓰기에서 빠른 속도가 나고, 하드웨어 성능에 비례하여 DB 성능도 올라가기때문에 범용적으로 사용학시에 적합하다.

<br/>
<br/>