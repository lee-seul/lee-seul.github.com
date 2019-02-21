---
layout: post
title: "Django with transaction.atomic() 사용시 주의점"
date: 2019-02-02 18:21:22 +0900
categories: [django]
encoding: UTF-8
---

<br>
<br>

### Django with transaction.atomic() 사용시 주의점

<br>

기본적으로 django는 transaction을 지원하지만, ATOMIC_REQUESTS가 False 이므로, ORM 쿼리 단위로 transaction이 관리된다. 
(create, delete, update, get_or_create ...)

그래서 어떤 특정 코드 뭉치에 DB transaction을 지원하려면, transaction.atomic과 같은 구문이 필요하다. 

이 때 주의할 점은 with transaction.atomic 블록 안에서 try-except를 하지말라는 것이다. (django 문서에도 언급되어 있다.)

try-except를 사용하고 싶다면 아래와 같이 처리해야 한다. 

```python

try:
    with transaction.atomic():
        somthing()

except:
    raise Exception()
```

---

<br>

만약 with transaction.atomic 블록 안에서 try-except 처리를 한다면 예상치 못한 오류가 생길 수가 있다.
(transaction.atomic 블록 내부에서 Raise 발생시 제대로 rollback을 수행하지 못하고, 다른 API 까지 고장나게 만든다.) 

django 테스트 코드 중 일부를 살펴보면 아래와 같은 코드가 있다. 

<br>

```python
    def test_mark_for_rollback_on_error_in_transaction(self):
        with transaction.atomic(savepoint=False):

            # Swallow the intentional error raised.
            with self.assertRaisesMessage(Exception, "Oops"):

                # Wrap in `mark_for_rollback_on_error` to check if the transaction is marked broken.
                with transaction.mark_for_rollback_on_error():

                    # Ensure that we are still in a good state.
                    self.assertFalse(transaction.get_rollback())

                    raise Exception("Oops")

                # Ensure that `mark_for_rollback_on_error` marked the transaction as broken …
                self.assertTrue(transaction.get_rollback())

            # … and further queries fail.
            msg = "You can't execute queries until the end of the 'atomic' block."
            with self.assertRaisesMessage(transaction.TransactionManagementError, msg):
                Reporter.objects.create()

        # Transaction errors are reset at the end of an transaction, so this should just work.
        Reporter.objects.create()
```

transaction.atomic 내부에서 Exception 발생 시 django는 해당 transaction을 rollback 하는데, 

exception이 내부에서 try-except 처리된 경우 제대로 rollback이 수행되지 못하여, 

connection 자체가 깨지는 문제가 생긴다. (정확하게 말하면 uwsgi worker가 고장이 난다)

transaction.atomic 내부 Exception 발생시  connection.needs_rollback = True로 변하고, 

이 값을 토대로 rollback이 이루어지는데, rollback이 제대로 실행되지 않으면서 

connection.needs_rollback의 값이 계속 해서 True로 남게 된다. 

이렇게 되면 해당 uwsgi worker로 연결되는 모든 tracnsaction을 사용하는 request 에서 

You can't execute queries until the end of the 'atomic' block.라는 메세지와 함께 TransactionManagementError를 발생시킨다. 

<br>

에러를 보고 받는 입장에서는 일부 서버의 일부 request만 계속해서 TransactionManagementError를 내뿜는 광경을 볼 수 있다.(오류가 발생하는 지점을 파악하기가 매우 힘들어진다.)

<br>

transaction.atomic() 내부에서는 try-except 를 사용하지말자. 써야만 한다면 try 블록이 transaction.atomic() 블록을 감싸도록 사용하자. 



<br>
<br>