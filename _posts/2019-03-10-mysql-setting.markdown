---
layout: post
title: "알아두면 좋은 MySQL 설정들"
date: 2019-03-10 02:54:52 +0900
categories: [backend]
encoding: UTF-8
---

<br>
<br>

### 알아두면 좋은 MySQL 설정들 

<br>

인스턴스에 MySQL을 설치해서 사용하든 RDS와 같은 SaaS를 사용하든 MySQL의 설정을 만져야 하는 경우가 있다.
그럴 때를 대비해서 몇 가지 중요한 설정 값들을 정리하고자 한다. 

<br>


여기서 설명할 설정값들이다.

- max_connections
- thread_cache_size
- wait_timeout
- tmp_table_size
- sort_buffer_size
- join_buffer_size
- read_rnd_buffer_size
- innodb_buffer_pool_size
- innodb_lock_wait_timeout
- innodb_flush_log_at_trx_commit



<br>

#### MySQL 설정 변수의 구분

<br>

**1. 글로벌 변수와 세션 변수**

설정 변수가 영향을 미치는 범위에 따른 분류이다. <br>
글로벌 변수의 경우 MySQL 서버에서 단 하나의 값을 가지며, 시스템에 영향을 미치는 변수이다. <br>
반대로 세션 변수는 각 세션에만 적용되는 값으로 커넥션 단위로 값을 변경할 수 있다. <br>

<br>

**2. 동적변수와 정적 변수**

동적 변수와 정적 변수는 변수를 값을 변경할 때 서버를 재시작해야 하는지 여부에 따른 분류이다. <br>
동적 변수의 경우 서버를 재시작하지 않고도 값을 변경할 수 있는데 반해 정적 변수는 서버를 재시작해야지만 값이 변경된다. 



-----

<br>

### 설정값들

<br>

#### max_connections

- 글로벌 변수
- 동적 변수


이 값은 MySQL 서버가 최대로 허용하는 연결 개수에 대한 설정이다. MySQL이 죽는 대부분의 경우가 max_connections와 관련이 있다. 

이 값을 낮게 설정했을 경우에는 성능상 문제가 되지만, 너무 높게 설정하는 경우 서버가 죽을 위험이 매우 커진다. 

MySQL 서버가 느려진 경우(트래픽이 몰렸거나 등의 이유로)에도 들어오는 요청을 받기 위해 커넥션을 계속해서 만들어 낸다. 

커넥션의 수가 늘어날 수록 서버가 죽을 확률이 높아지기 때문에 적정 값을 설정하는 것이 중요하다. 

평소 얼만큼의 커넥션이 사용되는지 모니터링한 후 최대값 정도로만 설정해주면 된다. 

만약 문제가 될 경우 동적 변수이기 때문에 손 쉽게 수정할 수 있기 때문에 그 때에 수정해주면 된다. 


<br>

#### thread_cache_size

- 글로벌 변수
- 동적 변수

커넥션이 서버와 클라이언트의 연결 자체를 의미한다면, 스레드는 커넥션으로부터 오는 작업을 처리하는 주체이다. 

thread_cache_size는 스레드를 얼마만큼 스레드 풀에 가지고 있을지 지정하는 값으로 스레드의 숫자를 말한다.

기본값은 0인데, 0으로 설정된 경우 커넥션이 생성될 때마다 스레드를 생성한다. 

스레드가 생성이 커넥션의 숫자보다 많은 경우 이 thread_cache_size 값을 올리는 것을 고려하면 된다. 
(Threads_created / Connections의 값이 1을 넘는 경우)



<br>

#### wait_timeout

- 글로벌 변수
- 동적 변수

연결된 클라이언트가 아무런 요청을 하지 않고 대기할 수  있는 최대 시간이다. 

초 단위로 설정하며, 이 시간을 넘는 경우 강제로 연결을 끊어버린다. 

기본 값이 8시간으로 매우 길기때문에, timeout으로 연결이 종료되는 경우 MySQL 서버의 문제보다는 로드 밸런서나 운영 체제 단에서의 설정 문제일 경우가 많다.

<br>

#### tmp_table_size

- 글로벌 변수
- 동적 변수

메모리에 저장되는 임시 테이블의 최대 크기를 결정하는 값이다. 

일반적으로 검색 결과를 담기 위해서 많이 쓰이며, 이 값을 넘는 경우 MyISAM 엔진에 값을 담게 된다. 


<br>

#### sort_buffer_size

- 세션 변수
- 동적 변수

MySQL에서 인덱스 없이 정렬을 수행할 경우 디스크 버퍼에서 데이터를 정렬한다. 

보통 MySQL에서 부하가 많이 생기는 작업이 정렬인데, sort_buffer_size를 통해 정렬에 얼마만큼의 메모리 공간을 할당할지 정할 수 있다.

단순히 생각하면 sort_buffer_size의 값이 큰 쪽이 좋을 것 같지만, 세션 변수이기때문에 여기에 설정한 값은 모든 세션에서 각각 사용하는 값이다. 

즉, sort_buffer_size의 크기가 커지면 커질수록 각 커넥션에 할당되어야 하는 메모리 크기가 커지므로 무조건적으로 값을 크게 설정하면 오히려 성능에 안좋은 영향을 미칠 수 있다. 

그리고 세션 변수이기떄문에 대량의 정렬 작업이 필요한 세션에서만 값을 바꾼 다음 정렬을 시도하는 방법도 있다.

<br>

#### join_buffer_size

- 세션 변수
- 동적 변수

이름은 join_buffer_size이지만 테이블이 조인될 때가 아닌 테이블 풀 스캔이 이루어질 때와 관련이 있는 변수이다.

이 값도 sort_buffer_size와 마찬가지로 세션 변수이므로 단순히 큰 값을 설정해서는 안된다.


<br>

#### read_rnd_buffer_size

- 세션 변수
- 동적 변수

MySQL에서 인덱스 없이 정렬할 때 Single-pass 또는 Two-pass라는 알고리즘을 사욜하는데, 그 중 Two-pass 알고리즘을 사용할 때 사용할 버퍼의 크기를 정하는 변수이다.

Two-pass 알고리즘은 정렬한 이후, 다시 값을 읽는 방식을 말한다.


<br>

#### innodb_buffer_pool_size

- 글로벌 변수
- 동적 변수(일부 과거 버전에서는 정적 변수)

InnoDB를 주로 사용한다면, 메모리의 최소 50%를 할당해야 할 정도로 중요한 변수이다. 

InnoDB 버퍼 풀은 캐싱과 메모리 버퍼의 역할을 동시에 수행한다. 

스레드가 사용할 메모리 영역을 계산한 후 그 값을 제외한 대부분의 값을 할당해주면 된다.

<br>

#### innodb_lock_wait_timeout

- 글로벌 변수
- 동적 변수(과거 일부 버전은 정적 변수)

Lock을 획득하기 위해서 기다리는 최대 시간을 설정하는 변수이다. 

Lock wait timeout exceed 에러와 관련이 있다.

<br>

#### innodb_flush_log_at_trx_commit

- 글로벌 변수
- 동적 변수

innodb_flush_log_at_trx_commit에는 InnoDB에서 로그를 버퍼에서 디스크로 얼마만큼 자주 옮겨쓸지를 설정한다.

대부분의 RDBMS의 부하는 디스크에 IO할 때 발생하므로, 성능과 연관이 있는 값이다. 

0~2까지의 값을 설정할 수 있는데, 0의 경우 커밋 기반이 아닌 1초마다 버퍼를 디스크로 옮겨 적는다. 

1의 경우는 커밋이 일어 날 때마다 이고, 2 일떄는 커밋이 일어날 때마다 디스크에 옮겨 적지만 버퍼를 비우지는 않는다.

반대로 0과 1일 때는 디스크에 옮긴 후 버퍼를 비운다. 

0의 경우 장애 발생시 최근 1초의 데이터를 유실할 수 있고, 1의 경우는 모든 트랜잭션을 기록했기때문에 안전하다. 

2의 경우에는 OS에 문제가 있는 것이 아니라면 안전하지만 OS에 장애가 있는거라면 역시 최근 1초 간의 데이터를 잃을 수 있다.

시스템마다 최상의 값을 고려해서 설정해야하는데 디스크 쓰기 작업이 적을수록 RDBMS의 성능이 향상되므로 트랜잭션이 1초에 1번 보다 잦다면 0또는 2가 성능상으로는 유리하다. 

반대로 트랜잭션이 1초에 1반 이하로 발생하는 시스템이라면 1이 무조건적으로 유리하다. 

0, 2는 데이터 유실의 위험이 존재하기 때문이다. 

<br>
<br>