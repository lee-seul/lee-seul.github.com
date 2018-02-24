---
layout: post
title: "Django, django_admin_log 테이블"
date: 2018-02-03 17:27:11 +0900
categories: [django, backend]
encoding: UTF-8
---

<br>


### django_admin_log

<br/>

Django에서 프로젝트를 진행시 첫 migrate 이후에 생성되는 테이블이 몇 가지 있는데 그 중 하나가
django_admin_log이다 admin 페이지에서 이루어지는 변경 사항을 log로 남기는 테이블인데

conent_type_id, object_id, action_time, object_repr, action_flag, user_id 등을 저장한다.

<br/>

- content_type_id: 어떤 모델인지를 알 수 있게 해준다. django_content_type이라는 테이블을 참조하며, 생성된 순서대로 저장된다. 

- object_id: content_type_id에 해당하는 모델의 id값을 저장한다. content_type_id와 조합하면 어떤 모델의 id가 몇인 인스턴스가 변경되었는 지를 알 수 있다.

- action_flag: 1, 2, 3으로 구분되며 1은 추가, 2는 수정, 3은 삭제를 나타낸다

<br>


