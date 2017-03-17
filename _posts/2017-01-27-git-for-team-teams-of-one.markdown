---
layout: post
title: "Teams of one, 1인 팀을 위한 git"
date: 2017-01-27 21:41:33 +0900
categories: [git]
encoding: UTF-8
---

Git for Teams, 챕터5 내용을 정리한 것


### **1. Creating Local Repositories**

Git에서 저장소를 만드는 방법은 크게 3가지이다.

- 기존 저장소의 사본

```python
$ git clone {git repositoty URL}
```

- 빈 디렉토리(새로만들어진)

```python
$ git init # 프로젝트 루트에서 실행
```

- 기록되지 않은 파일을 가진 기존 디렉토리

```python
$ git init
$ git add --all # 또는 git add * 
$ git commit -m "{commit message}"
```

방법은 다르지만 모두 로컬 저장소를 생성하고 생성된 저장소의 파일들이 버전 관리에 들어간다.

---

- git History 확인하기 

```python
$ git log
$ git log --oneline
```

git log의 경우 저장소에 첫 커밋을 한 이후부터 마지막에 실행한 커밋까지 시간 역순으로 검토할 수 있다.
커밋 ID, 커밋한 주체, 날짜, 커밋 메세지 등을 볼 수 있다. 반면 --oneline이 붙으면 커밋 메세지와 커밋ID만을 
한줄에 보여준다.

<br/>
<br/>

### **2. Branch Working with Branches**


두 명령어 모두 로컬 브랜치의 목록을 보여주며, 현재 작업 중인 브랜치는 *가 앞에 붙는다.

```python
$ git branch
$ git branch --list
```

리모트 브랜치만을 보여주는 명령어이다.

```python
$ git branch --remote
```

로컬 브랜치와 리모트 브랜치 모두 보여주는 명령어이다. 리모트 브랜치의 경우 빨간색으로 표시된다.

```python
$ git branch --all
```

---

리모트 브랜치 목록은 자동으로 최신 상태가 유지되지 않기때문에 주기적으로 최신화를 해주어야한다.

```python
$ git fetch
```

git fetch 명령어로 모든 리모트 브랜치의 내용이 업데이트된다. 


브랜치는 독립적인 작업을 보장해주는데 이 때문에 여러 용도로 사용되어지며, 프로젝트의 규모가 클수록 브랜치의
종류와 수가 많아진다. 이런 경우 각 작업에 맞는 브랜치로 변경하여 작업하여야 하는데 이 때 사용되는 명령어가 checkout이다.

```python
$ git checkout {작업할 브랜치 명} 
$ git checkout -b {작업할 브랜치 명} # 새 branch를 만들고 해당 브랜치로 checkout
```

두번째 명령어의 경우 -b가 새로운 브랜치를 만들 때 사용하는 명령어이므로

```python
$ git branch {생성할 브랜치 명}
$ git checkout {작업할 브랜치 명} 
```
과 동일한 결과를 만든다. 


```python
$ git checkout --track {리모트 브랜치 명}
```
--track의 경우 새로 생성된 로컬 브랜치가 리모트 저장소와 접점 역할을 수행할 리모트 브랜치가 지정되어 있지 않기때문에
로컬 브랜치에서 push/pull했을 때 사용될 리모트 브랜치를 지정해주는 역할을 한다. 
(git push --set-upstream과 유사) --track을 붙여주면 로컬 브랜치를 트래킹할 리모트 브랜치가 지정되는데 이때 
리모트 브랜치를 로컬 브랜치의 upstream브랜치라고 한다.

<br/>
<br/>

### **3. Adding changes to a Repository**

git에서는 파일을 untracked, unmodified, modified(unstaged), staged 4가지로 생애주기를 구분한다.
아직 버전 관리 시스템에 등록되지 않은 untracked, 등록은 되었으나 아무런 변경 사항이 없는 상태를 
unmodified, 수정은 되었으나 staging area(준비 영역)에 포함되지 않은 상태를 modified, staging area에 포함된 staged 상태로 구분한다.
현재 상태는 버전 관리 시스템에 등록이 되었고 이후에 변경 사항이 있으나 아직 staging area에 포함되지 않은 modified상태이다.

modified(unstaged)와 staged된 파일은 git status라는 명령어로 알 수 있다.

```python
$ git status
```

---

변경된 파일들을 staging area에 등록할 때 사용하는 명령어가 add이다. 

```python
$ git add * # 현재 디렉토리에 있는 모든 파일과 디렉토리의 변경 사항을 반영
$ git add {파일 이름} # 특정 파일의 변경 사항만을 반영
$ git add *.txt # 확장자가 .txt인 파일만을 반영 

$ git add --update # 기존에 git으로 관리되고 있던 파일들만 반영
```

그리고 파일 단위가 아닌 파일 내 한줄 한줄을 분리하여 반영시킬 수 있다.

```python
$ git add --patch {파일 이름}
```
--patch를 사용할 경우 변경된 코드 한줄마다 staging area에 등록할 것인지 정할 수 있다.

그리고 staging area에 있는 내용들을 다시 unstaged시키기 위해서는 reset 명령어를 사용한다.

```python
$ git reset {브랜치 명} {unstaged하고 싶은 파일 명} # 특정 파일을 unstaged
$ git reset {브랜치 명} # 해당 브랜치 staging area에 있는 모든 내용을 unstaged
```

reset명령의 경우 변경한 사항을 다시 되돌리는 것은 아니며, 다음 번 commit에 포함될 내용을 수정하기 위한 것이다.

--- 

add를 통해 staging area에 등록된 내용들은 commit을 통해 하나의 작업 단위로 바뀌게 된다. 각 commit을 통해 프로젝트의 버전을 되돌리거나 수정할 수 있기때문에
각 commit은 구분되어야하며, 각 commit이 어떤 작업 내용을 포함하고 있는지 알 수 있어야한다. 이 때문에 작성하는 것이 commit message이다.
프로젝트를 진행하는 사람이 1명이 아닐 경우 여러가지 규칙을 만들어 불필요한 커뮤니케이션을 줄일 수 있는데 그 중 하나가 commit message 작성법이다. - git History 확인하기 

```python
$ git commit -m "{commit message}"
$ git commit --amend
```

일반적으로 -m을 활용하여 commit message를 작성할 수 있다. -m을 제외하고 명령어를 실행 경우 기본적으로 설정된 에디터가 실행되어 commit message를 작성할 수 있다. 이미 작성된 
commit message의 경우에도 --ammend를 통해 추가 작성이 가능하다.


--- 

git add --all 등을 사용하여 변경된 전체 파일을 반영하고 싶지만, 의미 없는 파일이거나 공개되면 안되는 파일이 존재하는 경우 
/.gitignore 파일을 통해 버전 관리에서 제외시킬 수 있다.

```python
$ git config --global core.excludesfiles ~/.gitignore # 글로벌 .gitignore 파일 생성
```

git config는 git을 사용할 때 필요한 여러 설정을 바꾸거나 추가할 수 있는 명령어이다. 위에 명령어의 경우 글로벌하게 적용되기때문에 문제가 생길 여지가 있으므로,
프로젝트 디렉토리 루트에 .gitignore를 추가해서 제외시키고 싶은 파일의 이름이나 패턴을 지정하는 것이 좋다.

<br/>
<br/>

### **4. Working with Tags**

위에서 설명했듯이 각각의 commit은 구분되어야하며, 해당 commit의 작업을 알 수 있어야하는데 commit message만으로 부족하거나 보다 강력한
표시를 하고 싶다면 tag를 활용할 수 있다. tag는 commit에 alias를 주는 것이라고 이해해도 무방하다.

```python
$ git tag {tag content} {commit ID}
$ git tag # tag 목록 출력
```
특정 commit에 tag를 붙일 수 있고 태그만을 따로 출력해서 볼 수 있다. 


<br/>
<br/>

### **5. Connecting to Remote Repositories**

1인팀의 경우, 리모트 저장소는 로컬 저장소의 백업이라고 할 수 있다. 하지만 다수의 팀원이 모인 경우 
리모트 저장소를 통해 변경점들을 취합하고 프로젝트를 진행해 나갈 수 있다. 

하나의 로컬 저장소를 두고 여러개의 리모트 저장소를 둘 수 있는데 이 때 리모트 저장소를 추가로 연결하기위한 
방법을 알아보도록 한다. 

```python
$ git remote add {alias} {owner email}:{Repository name}
```
로컬 저장소를 리모트 저장소로 연결할 때는 alias가 필요한데 리모트 저장소를 clone하거나 한 경우에는 
origin으로 설정된다. 메일 주소는 리모트 저장소에 접근 권한이 있는 이메일을 입력하면 된다.
이렇게 연결된 리모트 저장소들은 --verbose를 통해 확인할 수 있다.

```python
$ git remote --verbose 
```

--- 

로컬 저장소에서 변경된 사항들을 리모트 저장소에 반영할 때에는 push 명령어를 통해 할 수 있는데 처음 push를 할 경우
해당 로컬 브랜치가 어느 리모트 브랜치에 push해야하는지 알려줘야 한다. 

```python
$ git push # upstream 브랜치가 이미 있을 때
$ git -push --set-upstream {upstream 브랜치} {로컬 브랜치} # upstream 브랜치 설정
```

--set-upstream을 해준 이후에는 git push만으로 작업을 업로드할 수 있다. upstream을 설정해주어도 
리모트 브랜치는 로컬 브랜치를 트래킹한다. (--track과 같은 효과)

---

특정 브랜치에서 진행하던 모든 작업을 마치고 메인 브랜치에 병합된 이후에는 (특정 작업)브랜치의 존재 이유가 사라진다.
더 이상 필요 없어진 브랜치는 삭제를 하여 정리할 필요가 있다. 

```python
$ git branch --delete {로컬 브랜치} # 로컬 브랜치 삭제
$ git push --delete {리모트 브랜치} {로컬 브랜치} # 리모트 브랜치 삭제
```


이상으로 Git for Team 5장의 내용을 모두 정리하였다. 이해한 내용을 바탕으로 작성한 것이라 
틀린점이 있을 수 있다.

<br/>
<br/>