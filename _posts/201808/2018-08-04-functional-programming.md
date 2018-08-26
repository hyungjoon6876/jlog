---
layout: post
title: 'Functional programming'
tags: [Java]
---
functional programming 은 자료처리를 수학적 함수의 계산으로 취급하고 상태와 가변 데이터를 멀리하는 프로그래밍 패러다임의 하나입니다.

## 함수형 프로그래밍

functional programming의 특징은 아래와 같습니다.

* 순수 함수 (pure function)
* 익명 함수 (anonymous function)
* 고계도 함수 (high order function)

## 순수 함수
순수한 함수는 부작용이 없는 함수 입니다.
외부 요인에 영향을 받지 않고 입력값에 따라 그에 맞는 결과 값을 반환하는 함수를 말합니다.  
아래의 예에서는 queue 의 상태 값에 따라 다양한 결과가 반환할수 있습니다. 
`addByQueue` method 는 순수 함수가 아닙니다.

```java
public int addByQueue(){
	int x = queue.pop();
	int y = queue.pop();

	return x + y;
}
````

`add()` method 는 x, y 입력값에 따라서 항상 일정한 값을반환하는 순수 함수입니다.

 add(2, 2) // 4 return
 add(3, 5) // 8 return  


```java
public int add(x, y){
	return x + y;
}
```

 ## 익명 함수

javascript 익명 함수의 예입니다.

```javascript
(() => console.log('annonymous function!'))
```

이러한 함수를 이름값 없이 표현한 함수 입니다.

```javascript
var f = function hello(){
	console.log('non-annonymous function!');
}
```

```scala
(x: Int) => x + 1
(x: Int, y: Int) => "x + y = " + (x+y) + "."
```

고계 함수
고계 함수는 입력값과 반환값을 함수로 입력하고 반환받을 수 있는 형태의 함수를 말합니다.
즉, 함수 또한 value로 여깁니다.

고계도 함수의 javascript 예입니다.
`map()` 의 parameter로 `value => console.log(\`value +2 : ${value + 2}\`) );` 형태의 함수형 parameter가 대입되었습니다.

```javascript
[1, 3, 5, 7, 9].map(value => console.log(`value + 2 : ${value +2}`) );
```

고계도 함수의 java 예입니다.
java 에서는 java8 부터의 @FunctionalInterface를 이용해 고계함수의 표현이 가능해 졌습니다.

`filter` 에 `s -> s > 2` , `map` 에 `s -> s * s`, `forEach` 에 `s -> System.out.println(s)`

java method 에 익명함수가 대입되었습니다.

```java
IntStream.of(1, 2, 3, 4, 5)
		.filter(s -> s > 2)
		.map(s -> s*s)
		.forEach(s ->System.out.println(s));
```

java  `Function<T, R>` interface로 함수를 반환 받을 수 있습니다.

```java
Function<Integer, String> f = (s) -> "[" + s + "]";

f.apply(3); // return "[3]"
f.apply(12); // return "[12]"
```

## 출처
 * [https://ko.wikipedia.org/wiki/%ED%95%A8%EC%88%98%ED%98%95_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D]/(https://ko.wikipedia.org/wiki/%ED%95%A8%EC%88%98%ED%98%95_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D)
 * [https://www.tutorialspoint.com/functional_programming/index.htm](https://www.tutorialspoint.com/functional_programming/index.htm)