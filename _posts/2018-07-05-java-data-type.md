---
layout: post
current: post
cover: assets/images/20180705/Java-logo-png.png
navigation: True
title: 'Java data type 정리'
date: 2018-07-05
tags: Java
class: post-template
subclass: 'post'
author: hj
---

# Java data type
프로그래밍 언어를 입문하면 가장 먼저 접하는것 중 하나는 해당 언어의 data type 일 것입니다.  
Java를 처음 접했을때의 느낌을 되살려보고, 기본기를 좀 더 탄탄하기 위해서 data type 에 대해서 정리해 보았습니다.

## Java data type 종류

	1. Primitive Data type
	2. Reference/Object Data type

	Java에는 2가지 data type 이 존재합니다. 
	Primitive 데이터 타입과 Reference/Object 타입이 존재합니다.
	Primitive type의 경우 메모리에 값 자체를 저장하지만 Referece/Object data type 의 경우 힙 영역에서 주소값이 참조된 형태로 Java GC에 의해 관리됩니다.

## Primitive data type
Java에는 8가지 primitive type이 존재합니다.   
null 값을 할당 할수 없으며 스택 메모리에 값이 저장관리 됩니다.  
primitive type을 클래스화 한 Wrapper class 가 존재하며 null값 할당이 가능하다.

### byte
 * 8bit 
 * 부호있는 정수
 * default = 0
 * Wrapper class `Byte`
### short
 * 16bit
 * 부호있는 정수
 * default = 0
 * Wrapper class  `Shrot`
### int
 * 32bit
 * 부호있는 정수
 * default = 0
 * Wrapper class `Integer`
### long
 * 64bit
 * 부호있는 정수
 * default = 0L , `L`의 의미는 명시적으로 long type 임을 보여주기 위함
 * Wrapper class `Long`
### float
 * 32bit
 * 실수
 * 부동소수점 방식 메모리 저장
 * 화폐와 같은 곳에 정확한 값이 필요한 곳에 사용하면 안됨
 * default = 0.0f, `f`는 float type의 명시적 표현
 * Wrapper class `Float`
### double
 * 64bit
 * 실수
 * 부동소수점 방식 메모리 저장
 * 화폐와 같은 곳에 정확한 값이 필요한 곳에 사용하면 안됨
 * default = 0.0d, `d`는 float type의 명시적 표현
 * Wrapper class `Double`
### boolean
 * true 또는 false
 * default = false
 * Wrapper class `Boolean`
### char
 * 16bit 
 * 유니 코드 문자
 * default = '\u0000'
 * Wrapper class `Character`

## Reference/Object data type
Object class 를 상속한 모든 class 들은 Reference type 입니다.
`class type`, `interface type`, `array type`, `Enum type` 등의 종류가 있습니다.
`new` 연산자를 통해 생성되는 인스턴스들로 Java Heap 영역에 Address 값이 관리되어 집니다. 
c, c++의 pointer 와 비슷해보일수 있으나 차이가 있습니다.

예외적으로 `String class`는 new 연산 없이도 인스턴스를 생성할수 있는 reference data type 입니다.

``` java
class Baby{
	private String name;
	
	public String getName(){
		return this.name;
	}

	public void setName(String name){
		this.name = name;
	}
}

Baby b = new Baby();
```

여기서 Baby class 는 'clas type'의 Reference data type 입니다.

```java
interface BabyHandler{
	void handle();
}
```

BabyHandler interface 는 'interface type'의 Reference data type 입니다.

## 정리
간단히 Java data type에 대해서 다시 정리해 보았습니다.  
입문할때 그냥 넘어갔었던 부분들이 있었지만 다시 정리해보며 추가적으로 정리해야 할 부분을 찾았습니다.  
JVM 영역에 대한 이해와, GC 메카니즘 정리가 필요해 보입니다.

---

### 출처
* [https://stackoverflow.com/questions/2721546/why-dont-java-generics-support-primitive-types](https://stackoverflow.com/questions/2721546/why-dont-java-generics-support-primitive-types)
*   [https://www.tutorialspoint.com/java/java_basic_datatypes.htm](https://www.tutorialspoint.com/java/java_basic_datatypes.htm)
* [http://hipercube.tistory.com/entry/JAVA-Primitive-type-Reference-type](http://hipercube.tistory.com/entry/JAVA-Primitive-type-Reference-type)

	