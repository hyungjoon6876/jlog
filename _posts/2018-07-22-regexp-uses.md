---
layout: post
title: '정규 표현식 활용 v0.1'
tags: [tip, regexp]
---
앞서 정리했던 [정규식 정리]({{ site.baseurl }}/2018/07/10/regexp.html) 를 토대로 실제로 활용할수 있는 몇가지 패턴들을 정리해보겠습니다. 현재 간단히 생각나는 몇가지 패턴만 정리하였는데 생각날때마다 확장해 보겠습니다.

## 정규식의 활용 예제
다양한 패턴의 정규식 활용 예제를 정리해 보겠습니다.  
javascript console 에서 작성하고 테스트하였습니다.

### 정수

* 입력값 : -7, -6, -5, 0, 1, +1, 2, +2,  3, +3...

```javascript
const pattern = /^(([-+]?[1-9]{1}[\d]*)|0)$/g;

'-7'.match(pattern);
'0'.match(pattern);
'+3'.match(pattern);
'+0'.match(pattern);
'3.0'.match(pattern);
```

### 실수

```javascript
const pattern = /^[-+]?(([1-9][0-9]*)|([0-9]))\.[0-9][0-9]*$/g; 

'1.2000'.match(pattern);
'0.1'.match(pattern);
'00.22'.match(pattern);
'0.003'.match(pattern);
'12.12'.match(pattern);
```


### http request header 요청 분리 

```javascript
const test = 'PUT /skin/skin_1/skin/dd/d.s HTTP/1.1';
const array = test.split(' ');
const methodPattern = /^((POST)|(GET)|(DELETE)|(PUT))/g;
const urlPattern = /^(\/[\w]*)*(\/[\w]*\.[\w]*){0,1}$/g;
const httpVersionPattern = /^HTTP\/((1\.0)|(1\.1)|(2\.0))$/g;

array[0].match(methodPattern);
array[1].match(urlPattern);
array[2].match(httpVersionPattern);

```

### 날짜
yyyy.mm.dd 또는 yyyy-mm-dd

```javascript
const pattern1 = /^[1-9][0-9]{3}\-([0][1-9]|[1][0-2])\-([0-2][0-9]|[3][0-1])/g;
const pattern2 = /^[1-9][0-9]{3}\.([0][1-9]|[1][0-2])\.([0-2][0-9]|[3][0-1])/g

'1988-09-30'.match(pattern1);
'1988-09-30'.match(pattern2);
'1988-09-32'.match(pattern1);
'1988-09-32'.match(pattern2);

```

### 이미지 파일

```javascript
//특수문자 미포함
const pattern = /^[ㄱ-ㅎ가-힣a-zA-Z0-9\w]*\.((jpg)|(jpeg)|(bmp)|(gif)|(png)){1}$/g;

'한1_글test.png'.match(pattern);
'한1_글test.jpeg'.match(pattern);
'한1_글test.bmp'.match(pattern);
````

### ip

