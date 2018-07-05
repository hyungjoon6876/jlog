---
layout: post
current: post
cover: assets/images/20180705/garbage-collection.png
navigation: True
title: 'Java data type 정리'
date: 2018-07-05
tags: Java
class: post-template
subclass: 'post'
author: hj
---
추가적으로 GC 방법에 따라 4가지 방식의 reference type이 존재합니다.  
GC 메카니즘에서 메모리에대한 회수는 메모리 참조 뿐 아니라 레퍼런스 타입 유형에 따라 의존하기 때문에 알아 두는것이 좋을것 같습니다.
### Strong referecne
```java
Baby baby = new Baby()
```
Baby 객체를 생성했습니다.
### Weak reference
### Soft reference
### Phantom reference