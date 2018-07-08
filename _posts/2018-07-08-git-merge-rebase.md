---
layout: post
title: 'git merge와 rebase'
tags: [git,github]
---

# git merge와 rebase
`git`을 사용해오면서 가장 큰 장점중의 하나로 꼽을 수 있는것이 바로 `branch`입니다.  
`git`에서는 버전들을 스냅샷 형태로 관리하기 때문에 동시에 여러개의 `branch`를 관리할수 있습니다.  
이러한 `branch`를 통합하는 기능에는 `merge`와 `rebase`가 있는데, 이 두가지 기능의 차이점과 사용방법에 대해 알아보겠습니다.

## git merge
간단히 예제를 통해 정리해 보았습니다.


```git
git clone https://github.com/eockim/merge-rebase.git
```

```javascript
// merge-rebase.js
const master = function masterBranch(){

    const master = 1;
    console.log(`master branch : ${master}`);

    return master;

};
```

```git
git commit -m init
git push
git checkout -b merge-b
```

`master branch` 에서 merge-reabse.js 파일을 만들어 commit, push 후에 `merge-b` branch를 checkout 받습니다.

```javascript
// merge-rebase.js : mBranch function 추가
const master = function masterBranch(){

    const master = 1;
    console.log(`master branch : ${master}`);

    return master;

};

const mBranch = function mergeBranch(){
    
    const merge = 'merge-b';
    console.log(`branch : ${merge}`);

    return merge;

}
```

```git
git commit -m modify js
git push
```

merge-rebase.js 파일에 mBranch function을 추가해서 commit, push 합니다.

```git
git checkout master
git merge merge-b
```

merge 당하는 branch 를 checkout 합니다.

```log
Updating 1b56bfa..fe9dc5b
Fast-forward
 merge-rebase.js | 11 ++++++++++-
 1 file changed, 10 insertions(+), 1 deletion(-)
```

 `git merge` 명령을 실행하면 `Fast-forward` 메세지가 출력 됩니다.
 여기서 `Fast-forward` 는 git merging 방식으로 간단히 설명하면 `master` branch에서 `merge-b` 브랜치를 merge할 때 `merge-b`가 `mster` 이후의 커밋을 가리키고 있으면  `master`가 `merge-b`의 커밋을 가리키게 하는 것입니다.

![fast-forward]({{ site.baseurl }}/assets/img/20180708/fast-forward.png)  

다시 새로운 branch를 만들고 merge 해보겠습니다.

```git
git checkout -b merge-b-1
```

```jvascript
const master = function masterBranch(){

    const master = 1;
    console.log(`master branch : ${master}`);

    return master;

};

const mBranch = function mergeBranch(){
    
    const merge = 'merge-b-1';
    console.log(`branch : ${merge}`);

    return `#`+merge+`#`;

}
```

```git
git commit -m const merge
git commit -m mBranch function
git push
```

`merge-b-1`branch 를 새로 만들고 mBranch function을 수정후 commit 하였습니다.  
임의로 2번으로 나누어서 각각 commit하였습니다.

```git
git checkout master
```

```javascript
const master = function masterBranch(){

    const master = 100;
    console.log(`master branch : ${master}`);

    return master;

};

const mBranch = function mergeBranch(){
    
    const merge = 'merge-b';
    console.log(`branch : ${merge}`);

    return merge;

}
```

```git
git commit -m const master
git push
```

```log
$git log --branches --graph  --oneline

* 836112e (HEAD -> master) const master
| * 6aa25f4 (merge-b-1) mBranch function
| * 28e3629 const merge
|/  
* fe9dc5b (origin/merge-b, origin/master, origin/HEAD, merge-b) modify js
* 1b56bfa init js
* 19b201c Initial commit
```

`master` branch checkout 후 master function의 `master` 변수를 100 으로 수정 후 커밋 push 하였습니다.  
`git log`로 brnach graph를 확인해 보면 `merge-b-1` branch 분기 후 `master` branch 에서 commit 이 있었고 `merge-b-1` branch와 merge한다면 아까 같은 `Fast-forward` 방식은 발생하지 않습니다.

```git
git checkout master
git merge merge-b-1
```

```log
$it log --branches --graph  --oneline

*   41f137e (HEAD -> master, origin/master, origin/HEAD) merge
|\  
| * 6aa25f4 (merge-b-1) mBranch function
| * 28e3629 const merge
* | 836112e const master
|/  
* fe9dc5b (origin/merge-b, merge-b) modify js
* 1b56bfa init js
* 19b201c Initial commit
```

`3-way-merge`라고 하는데 현재 `master`, `merge-b-1` branch 커밋, 공통 조상 커밋 3가지를 merge 합니다.  
merge에 대한 결과를 만들고 `master` 브랜치가 그 커밋을 가리키도록 이동합니다.  
`merge commit` 이라고 합니다.  
`merge commit`은 history에 대한 추적이 가능하다는 장점이 있습니다.

![rainbow-branch]({{ site.baseurl }}/assets/img/20180708/rainbow.png)  

하지만 이런경우가 있을수도 있습니다. merge 할때마다 merge commit 이 생기다 보니 너무 복잡해졌습니다.  
`rebase`는 merge commit 없이 merge를 수행합니다.

## git rebase
`merge` 와 같은 방식으로 새로운 branch를 만들고 `rebase`하겠습니다.

```git
git branch -b rebase-1
```

```javascript
//merge-rebase.js : rBranch function 추가
const rBranch = function rebaseBranch(){
    const rebase = 'rebase-1';
    console.log(`branch : ${merge}`);

    return rebase;
}
```

```git
git commit -m rebase init
git push
```

```git
git checkout master
```

```javascript
//merge-rebase.js : const master 변경
const master = function masterBranch(){

    const master = 1000;
    console.log(`master branch : ${master}`);

    return master;

};

```

```git
git commit -m const master
git push
```

```log
$ git log --branches --graph --oneline

* c94a743 (HEAD -> master) const master
* 8f5a2af (origin/master, origin/HEAD) const master
| * 2114592 (origin/rebase-1, rebase-1) rebase init
|/  
*   41f137e merge
```

같은 방식으로 `rebase-1` branch 분기후 `master` branch에 새로운 commit 을 하였습니다.

```git
git checkout master
git rebase rebase-1
```

```log
$ git log --branches --graph --oneline

* 87f4258 (HEAD -> master) const master
* 6d663eb const master
* 2114592 (origin/rebase-1, rebase-1) rebase init
*   41f137e merge
```

git log를 확인해보니 `rebase` 에서는 `Fast-forward` 방식으로 merge가 된걸 알수있습니다.  
`rebase`는 push전 불필요한 commit 을 정리하는 용도로 유용합니다.

---
## 참조
* [https://namu.wiki/w/Git](https://namu.wiki/w/Git)
* [https://sandofsky.com/images/fast_forward.pdf](https://sandofsky.com/images/fast_forward.pdf)
* [https://git-scm.com/book/ko/v1/Git-%EB%B8%8C%EB%9E%9C%EC%B9%98-%EB%B8%8C%EB%9E%9C%EC%B9%98%EC%99%80-Merge%EC%9D%98-%EA%B8%B0%EC%B4%88](https://git-scm.com/book/ko/v1/Git-%EB%B8%8C%EB%9E%9C%EC%B9%98-%EB%B8%8C%EB%9E%9C%EC%B9%98%EC%99%80-Merge%EC%9D%98-%EA%B8%B0%EC%B4%88)
*[https://git-scm.com/book/ko/v1/Git-%EB%B8%8C%EB%9E%9C%EC%B9%98-Rebase%ED%95%98%EA%B8%B0](https://git-scm.com/book/ko/v1/Git-%EB%B8%8C%EB%9E%9C%EC%B9%98-Rebase%ED%95%98%EA%B8%B0)