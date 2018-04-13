---
layout: post
title: "Github에서 Two-factor authentication(이중 인증) 사용하기"
date: 2018-04-13 20:46:51 +0900
categories: [other, git]
encoding: UTF-8
---

<br>

### Two-factor authentication(2FA)?

2FA란 2중 인증, 즉 아이디/비밀번호 이외에 인증 수단을 추가하여 계정 해킹을 방지하는 기법이다. <br>
요즘 세상에서는 중요한 사이트의 계정이라면 필수적으로 설정을 해야한다. <br> 
Github 또한 2FA를 지원하는데 Github에서 2FA를 설정하고 커맨드 라인에서 2FA 설정 이전과 동일하게 사용하기 위한 방법을 정리해보고자 한다.


<br>

### 2FA 설정

<br>

[Github 계정 설정 페이지](https://github.com/settings/profile)

위 계정 설정 페이지로 가면 왼쪽에 메뉴에서 **Security**를 클릭한다. 

<br>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/github_2FA_1.png)

<br>

Security에 가면 Recovery tokens 아래에 Two-factor authentication 이라는 항목이 있는 것을 볼 수 있다.

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/github_2FA_2.png)

<br>

**Set up two-factor authentication**을 클릭한다. 

<br>


![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/github_2FA_3.png)

<br>

**Set up using an app**와 **Set up using SMS** 2 가지 중에서 선택 할 수 있는데 편한 것을 선택하면 된다. 

이 글 에서는 **Set up using SMS**를 선택한다. 


<br>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/github_2FA_4.png)


<br>

그러고 나면 Recovery codes라는 것이 나오는데 2FA에 필요한 모든 인증 수단이 사용 불가능 할 때 사용할 수 있는 코드이다. 

아래 Print, Download 등 중에서 1 가지를 실행해야 Next 버튼이 활성화 된다.


<br>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/github_2FA_5.png)


<br>

SMS를 선택했기 때문에 위와 같은 화면이 보인다. SMS를 활용해서 인증하면 2FA 설정이 모두 끝난다. 



<br>
<br>

설정이 모두 끝난 다음 커밋을 리모트 레포지토리에 반영하기 위해 푸시를 하였더니 

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/github_2FA_6.png)


<br>

위와 같이 인증이 실패하는 문제가 있었다. 

이 문제의 해결은 Personal access token을 활용하면 된다. 

<br>


[Github 계정 설정 페이지](https://github.com/settings/profile)

다시 계정 설정 페이지로 가서 **Developer settings**를 선택한다. 

<br>

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/github_2FA_7.png)

<br>

Developer settings에서 Personal access tokens를 선택하고 Generate new token을 누른다. 

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/github_2FA_8.png)


<br>

그러면 아래와 같은 화면이 나올 텐데 tile과 필요한 권한을 선택하고 Generate token을 선택한다.  

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/github_2FA_9.png)



<br>


그러면 아까의 Personal access tokens 에서 새로 만들어진 token이 보이고 복사할 수 있는 버튼이 나온다. 


![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/github_2FA_10.png)


복사한 후에 커맨드 라인에서 패스워드 대신에 입력하면 git push가 정상적으로 이루어진다. 






















