---
layout: post
title: "Kill query in Amazon RDS for MySQL"
date: 2019-10-21 20:29:10 +0900
categories: [backend]
encoding: UTF-8
---

<br>
<br>

### MySQL RDS에서 프로시저를 활용한 쿼리 죽이기

<br>

RDS 기반의 MySQL을 서비스에 운영 중이라면 종종 kill 명령어로 죽지 않는 쿼리들을 만날 수 있다.

일반적으로는 현재 접속한 계정이 아닌 다른 계정에서 날린 쿼리를 kill 할려고 할 때인데, 

이럴 경우 아래와 같은 에러 메시지를 볼 수 있다.

```
Error Code: 1095. You are not owner of thread 341
```

이런 경우 해당 유저로 접속이 가능하다면 해당 유저로 접속한 후 kill 명령을 실행하면 되겠지만, 

접속 정보를 알 수 없는 유저의 경우 매우 난감한 상황이 생길 수 있다. 

이런 문제를 해결하기 위해 RDS에는 많은 종류의 프로시저들을 미리 만들어 두었는데, 

Error code 1095의 경우 mysql.rds_kill 또는 mysql.rds_kill_query 프로시저로 해결이 가능하다. 

두 가지다 인자로 쿼리를 실행하고 있는 쓰레드의 id를 넘겨주면 해당 쓰레드에서 돌고 있는 쿼리 혹은 쓰레드를 중지시켜 준다. 

```
CALL mysql.rds_kill(341)
```

또는
```
CALL mysql.rds_kill_query(341)
```

같은 형태로 사용할 수 있다. 

이 밖에도 수십개의 프로시저가 있으므로 상황에 맞게 선택해서 사용하면 된다.


<br>
<br>