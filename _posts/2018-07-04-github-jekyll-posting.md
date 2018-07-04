---
layout: post
current: post
cover: assets/images/jekyll_github.jpg
navigation: True
title: 'github pages jekyll posting 하기'
date: 2018-07-04
tags: [jekyll, github]
class: post-template
subclass: 'post'
author: hj
---

Markdown은 텍스트기반의 마크업 언어로 쉽게 쓸수있고, HTML로 변환이 가능하다는 특징이 있습니다.  
github을 이용해 봤으면 RADEME.md 파일을 본적이 있을것입니다.  
github 에서는 repository에 관한 정보를 md를 활용하고 있습니다.  
Markdown 에 대한 소개와 문법은 위키, 다른 기술 블로그에서 상세히 다루고 있으니 여기서는 간단한 설명으로 넘어가고
jekyll posting 방법 

---

## jekyll
jekyll 은 Markdown 을 활용해 정적인 웹사이트를 만들어주는 ruby 기반의 도구입니다.  
ruby를 꼭 알지 못하더라도 멋진 웹사이트 만들수 있습니다.  
댓글, 게시글 관리등 데이터베이스에 대해서 신경쓸 필요가 없는 심플함이 가장큰 장점으로 보여집니다.

## Markdown
Markdown은 텍스트기반의 마크업 언어로 쉽게 쓸수있고, HTML로 변환이 가능하다는 특징이 있습니다.  
github을 이용해 봤으면 RADEME.md 파일을 본적이 있을것입니다.
github 에서는 repository에 관한 정보를 md를 활용하고 있습니다.

### Markdown 문법
**#** 또는 **\*** 를 주로 사용한 문법으로 매우 직관적이고 간단합니다.
간단히 몇개정도만 정리하고, 더 필요할때는 [GitHub Markdown syntax](https://guides.github.com/pdfs/markdown-cheatsheet-online.pdf)
#### heading

```
# This is an <h1> tag
## This is an <h2> tag
###### This is an <h6> tag
```
> # This is an <h1> tag
> ## This is an <h2> tag
> ###### This is an <h6> tag

### unordered lists
```
* list1
* list2
    * list 2-1
    * list 2-2
        *list 2-2-1
```
> * list1
> * list2
>    * list 2-1
>    * list 2-2
>        * list 2-2-1

### ordered lists
```
1. Item 1
2. Item 2
3. Item 3
 * Item 3a
 * Item 3b
```
> 1. Item 1
> 2. Item 2
> 3. Item 3
>   * Item 3-1
>   * Item 3-2

### emphasis
```
**텍스트 강조**
__이것도 텍스트 강조__
```

> **텍스트 강조**  
> __이것도 텍스트 강조__

### 줄바꿈

markdown 에서 줄바꿈은 `space bar` 2번 입력

## jekyll posting

이제 markdown 문법을 알았으니, posting을 할수 있습니다.  
`_posts` 디렉토리에 md 파일을 저장합니다.  
파일명 형식은 `yyyy-mm-dd-tiltle.md` 입니다.  
예를 들어 현재 게시글을 저장한다면 `2018-07-04-github-jekyll-posting.md` 생성후 저장합니다.  

jekyll 에서는 머리말 이라는 기능을 제공합니다.
```
---
layout: post
current: post
cover: assets/images/jekyll_github.jpg
navigation: True
title: 'github pages jekyll posting 하기'
date: 2018-07-04
tags: [jekyll, github]
class: post-template
subclass: 'post'
author: hj
---
```
파일의 첫부분에 `---` 사이에 `YAML` 형식의 구문을 사용해 __사전 정의 전역변수__ 나 __사용자 정의 변수__ 를 접근 활용할수 있습니다.  
[Jekyll 메뉴얼](https://jekyllrb-ko.github.io/docs/frontmatter/)

## github pages 에 posting 배포 

`git add 22018-07-04-github-jekyll-posting.md`  
`git commit -m 'new posting' `  
`git push`  

push 까지 완료 하면 `https://username.github.io` 에서 새롭게 등록된 post를 확인 할 수 있습니다.

## 정리
github pages 서비스에서는 jekyll을 내부엔진으로 사용한다고 하니 jekyll을 사용하지 않을 이유가 없어보입니다.  
tistory, daum, naver 등 각종 블로그 서비스들 중 jekyll을 선택한이유는 markdown 기반의 심플함에 가장 끌렸던 
이유 인것 같습니다.  
여태 블로그 운영하는것을 고민만했는데, 본격적으로 블로그를 시작하게 했고 앞으로 플러그인들이나 커스터마이징을 통해 블로그를 멋지게 꾸며나갈수있길.