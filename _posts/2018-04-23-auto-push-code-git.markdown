---
layout: post
title: "shell script를 활용해 코드 자동으로 push 하기"
date: 2018-04-23 19:46:18 +0900
categories: [linux, other, git]
encoding: utf-8
---

<br>


코드를 변경할 때마다 테스트를 돌리는 것이 귀찮아서 git alias를 활용해 test에 통과하면 자동으로 push를 시키고 그게 아니라면 
취소 메세지를 보이게 하는 스크립트를 작성하였다. 

작성하다보니 이것 저것 추가로 되면 좋을 것 같아서 코드가 변경되면 자동으로 테스트를 돌리고 테스트가 성공하면 푸시를 하도록 하는 기능과 
자동으로 푸시를 하다보니 커밋 메세지를 입력할 수 없어 변경이 일어난 파일의 이름과 추가인지 삭제인지, 수정인지에 대한 내용을 
커밋 메세지에 자동으로 추가하도록 하였다. 

<br>
<br>
<br>


#### 1. git alias 를 통해 새로운 명령어를 등록 

<br>


```python
$ git config --global alias.auto-test-push '!auto-test-push.sh'
```

<br>

위처럼 입력하면 git auto-test-push라는 명령어 실행시 auto-test-push.sh라는 쉘 스크립트가 실행됨


<br>
<br>

#### 2. auto-test-push.sh 생성

<br>


```shell

# auto-test-push.sh 생성 

$ vim auto-test-push.sh
```

<br>


```shell
$ chmod 744 auto-test-push.sh
```

<br>

```shell
#!/bin/zsh


echo ""
echo ""
echo "#######################################################################################"
echo "#################################### run test #########################################"
echo "#######################################################################################"
echo ""
echo ""

python3 -m pytest --cov=.

if [  "$?" = "0" ]; then
    echo ""
    echo ""
    echo "###################################################################################"
    echo "################################# git push ########################################"
    echo "###################################################################################"
    echo ""
    echo ""
    
    message="$1"
    if [ $1 = '' ]; then
        message="auto commit and push"
    fi 

    git add .
    git commit -am "$message"
    git push 

else
    echo ""
    echo ""
    echo "################################### test fail ####################################"
    echo ""
    echo ""

    exit 1
fi

```

<br>

위 코드에서 python3 -m pytest --cov=. 은 python에서 pytest를 활용한 테스트를 수행하기위한 내용으로 일반적인 상황에서는 주석처리 하면 된다.


그 밑에 if [ "$?" = "0" ]; then은 pytest가 성공했는지를 판단하는 코드이다. $?이 0이 아니라면 테스트가 실패한 경우이므로 push하지 않는다.


<br>
<br>

#### 3. git auto-test-push 없이 코드가 변경될 때마다 테스트 후 push 실행 


<br>


watch라는 것을 활용하면 간단하게 구현 할 수 있다. 

watch는 모니터링 툴인데 지정된 시간마다 실행하고 싶은 명령어를 실행하여 로그로 남겨 준다. 


<br>


watch라는 명령어를 쳤을 때 해당 명령어가 동작하지 않는다면 


```shell

# watch 설치

$ brew install watch
```

<br>


```shell
$ watch -n 10 -g -d git status
```

<br>

위의 명령어는 10초마다 git status라는 명령어를 실행하고 그 명령어의 실행 결과가 변경되면 종료하는 명령어이다. 

-n 10처럼 숫자를 적어주면 뒤에 적힌 숫자만큼의 시간마다 실행된다. 

-d 뒤에는 실행할 명령어를 적어주면 되고, -g의 경우 결과가 이전과 다를 경우 watch를 종료시킨다. 


<br>

이미 수정된 파일이 있는 상태에서 실행한 후 해당 파일을 다시 수정하는 경우는 반영이 되지 않지만, 

실행 전에 로컬과 리모트를 동일하게 만든 이후라면 해당 명령어가 아주 잘 실행된다. 

<br>


변경된 코드가 있으면 auto-test-push.sh가 실행되는 쉘 스크립트를 작성한다. 


```shell
$ vim auto-repo-change-check.sh 
```

<br>


```shell
#!/bin/zsh


watch -n 10 -g -d git status

./auto-test-push.sh 
```

<br>

그리고 반복적으로 실행되도록 auto-test-push.sh 젤 아래에도 auto-repo-change-check.sh 실행 코드를 추가한다. 

<br>


```shell
#!/bin/zsh


echo ""
echo ""
echo "#######################################################################################"
echo "#################################### run test #########################################"
echo "#######################################################################################"
echo ""
echo ""

python3 -m pytest --cov=.

if [  "$?" = "0" ]; then
    echo ""
    echo ""
    echo "###################################################################################"
    echo "################################# git push ########################################"
    echo "###################################################################################"
    echo ""
    echo ""
    
    message="$1"
    if [ $1 = '' ]; then
        message="auto commit and push"
    fi 

    git add .
    git commit -am "$message"
    git push 

else
    echo ""
    echo ""
    echo "################################### test fail ####################################"
    echo ""
    echo ""

    exit 1
fi


./auto-repo-change-check.sh  # 추가
```

<br>
<br>

#### 4. 커밋 메세지 추가


<br>

auto-test-push.sh의 경우 처음부터 파라메타로 커밋 메세지를 받을 수 있도록 구성하였는데 

auto-repo-change-check.sh로 실행할 경우 커밋 메세지가 "auto commit and push"로 입력되는 문제가 있다. 

<br>


```shell
#!/bin/zsh


watch -n 10 -g -d git status

m="$(git status --short)"

./auto-test-push.sh $m 
```

<br>

위처럼 바꾸면 어떤 파일이 수정되었는지 여부는 커밋 메세지에서 확인할 수 있다. 


<br>
<br>

**커밋 메세지에는 해당 커밋이 뭘한 것인지를 알 수 있어야 하므로 커밋 메세지 부분을 조금 더 개발하거나 auto-test-push.sh만을 단독으로 사용하기를 권한다.**

<br>
<br>