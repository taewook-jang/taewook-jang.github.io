---
layout: post
title: Git Branch 이름 변경하기
categories: [Git]
tags: [Git, Git Branch]
fullview: false
comments: true
published: true
---

 이미 생성한 Branch가 아래와 같이 생성되어 있는 Branch의 이름을 변경하는 방법을 정리합니다.

 **이번 글은 AndroidStudio의 터미널에서 실행하면, 로컬에 브런치 파일이 다운로드한 상태여야 합니다.**

 `(checkout as new local branch 상태에서 가능합니다.)`

![branch-01]

**참고자료** - [gist - lttlrck](https://gist.github.com/lttlrck/9628955)

 이미 생성한 Branch의 이름은 다음의 명령을 통해서 변경이 가능합니다.

 - [Android Studio GitHub 공유하기 및 사용하기](http://thdev.tech/androiddev/2016/09/13/Android-Studio-GitHub-Share.html)


<br />

### branch 이름 변경하기

먼저 local에 있는 branch의 이름을 변경합니다.

`git branch -m`의 명령어 뒤에 `변경전_branch_name`과 `새로운_branch_name`을 적고 엔터칩니다.

```
git branch -m 변경전_branch_name 새로운_branch_name
```


<br />

### 이전 branch 제거

변경하고, 이전 branch의 파일을 삭제합니다.

`git push origin :삭제할 이전 브런치 이름`으로 명령어를 실행합니다.

```
git push origin :old_branch
```

![branch-02]

삭제가 완료되면 다음과 같이 확인이 가능합니다.

![branch-03]


<br />

### 변경한 새로운 브런치를 업로드

브런치를 변경하였으니, 새로운 브런치를 업로드합니다.

`git push --set-upstream origin` 뒤에 `new_branch`를 push 합니다.

```
git push --set-upstream origin new_branch
```

![branch-04]

`upstream`이 완료되면 다음과 같이 추가 확인이 가능합니다.

![branch-05]



<br />

## 마무리

간단하게 GitHub의 branch 이름 변경하는 것을 정리하였습니다.

Android Studio에서 GitHub에 데이터를 공유하는 방법은 다음의 글을 참고하시면 되겠습니다.

- [Android Studio GitHub 공유하기 및 사용하기](http://thdev.tech/androiddev/2016/09/13/Android-Studio-GitHub-Share.html)



 [branch-01]: /images/2016/2016-12-19-Git-Branch-Name-Change/branch-01.png
[branch-02]: /images/2016/2016-12-19-Git-Branch-Name-Change/branch-02.png
[branch-03]: /images/2016/2016-12-19-Git-Branch-Name-Change/branch-03.png
[branch-04]: /images/2016/2016-12-19-Git-Branch-Name-Change/branch-04.png
[branch-05]: /images/2016/2016-12-19-Git-Branch-Name-Change/branch-05.png
