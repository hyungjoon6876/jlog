---
layout: post
title: 'Java String 분석'
tags: [Java, tip]
---

## String, StringBuilder/StringBuffer

String, StringBuilder/StringBuffer 클래스의 가장 큰 차이는 `immutable`, `muttable`의 차이입니다.

String Object 는 변경이 불가능한 객체입니다. 
일반적으로 사용방식에 2가지 방법이 있습니다.
`String str = ""` 또는 `String str = new String()` 입니다.  

`""`을 이용해 할당하는 방식은 내부적으로 heap 메모리의 `String constant pool`에 hashmap 형태의 자료구조에 저장합니다. 그외 `new` 를 이용한 방식의 문자열은 heap 메모리에 객체 형태로 저장 합니다.

![string constant pool]({{ site.baseurl }}/assets/img/201808/string_pool.png)

이러한 동작방식으로 인해 다음과 같은 코드의 결과가 발생하게 됩니다.

```
String str = "hello";
String str2 = "hello";

String str3 = new String("hello");

System.out.println(str == str2);
System.out.println(str == str3);

```

이러한 String 의 내부동작의 차이로 같은 문자열이지만 비교시 다른 결과를 반환합니다.

```bash
true
false
```

String 의 `intern()`, `equals`,  `equalsIgnoreCase` 를 이용해 비교하여야 원하는 결과 값을 얻을 수 있습니다.

```java
System.out.println(str === str3.intern()); // true
System.out.println(str.equals(str3)); // true
```

String 객체는 변하지 못하는 `immutable` 객체라고 하였습니다. 그런데 일반적으로 `concat()`, `substring()`, `replace()` method 나 `+` 연산을 통해 문자열을 마음대로 변화 시킬수 있습니다.  
`String` class 를 살펴보겠습니다.

> /** The value is used for character storage. */  
> private final char value[];

`String` class 의 value 는 char array 형태이고 `final`이 붙어 있는걸 보니 변화할수 없는 형태가 맞습니다.

`concat()` method 구현 코드를 살펴보겠습니다.  
`Arrays.copyOf` method 로 늘어날 길이만큼의 char array 를 할당하고, 배열에 문자열을 넣습니다.  
그리고 `new String(buf, true)` 새로운 String 객체를 생성하고  return 합니다.

```java
public String concat(String str) {
    int otherLen = str.length();
    if (otherLen == 0) {
        return this;
    }
    int len = value.length;
    char buf[] = Arrays.copyOf(value, len + otherLen);
    str.getChars(buf, len);
    
    return new String(buf, true);
}
```

`String` class 에서 문자열에 대한 연산은 `new String()` 동작이 일어납니다.
`+` 연산이 어떻게 일어나는지도 알아보겠습니다.  
java bytecode 를 통해 내부동작을 한번 확인해 보겠습니다.
다음과 같은 코드를 `javap` 로 분석해 보았습니다.

```java
String s = "";       
s+= "concat";
```

내부적으로 StringBuilder 객체를 생성하고 apeend 함수를 호출하고 있습니다.

```bash
7: new           #3                  // class java/lang/StringBuilder
10: dup
11: invokespecial #4                  // Method java/lang/StringBuilder."<init>":()V
14: aload_1
15: invokevirtual #5                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
18: ldc           #6                  // String concat
20: invokevirtual #5                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
23: invokevirtual #7                  // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
```

정리해보면 아래와 같이 동작합니다.

```java
StringBuilder builder = new StringBuilder();
builder.append("");
builder.append("concat");
String s = builder.toString();
```

Jdk 1.5부터는 String을 사용하여도 내부적으로 StringBuilder를 사용하도록 최적화가 되어있다고 알고 있었는데 제대로 동작하고 있음을 확인했습니다.  
잘못 알고 있던 부분도 있었습니다. 위와 같은 방식으로 동작하고 있다보니 반복문에서  String `+` 연산을 할경우 확연한 성능차이를 확인 할수 있습니다.  

String 객체에 10만번의 `+` 연산의 예입니다. 실행시간이 얼마나 걸리는지 확인 해보겠습니다.

```java
 @Test
    public void stringTest(){

        String result = "";
        long start = System.currentTimeMillis();

        for(int i = 0 ; i < 100000; i++){
            result += "test";
        }
        long end = System.currentTimeMillis();

        System.out.println("String exec time : " + (end - start));

    }
```

약 15초의 시간이 걸립니다.

```bash
String exec time : 15041
```

이번엔 String builder 객체에 똑같은 방법으로 test 해보겠습니다.

```java
 @Test
    public void stringBuilderTest(){

        StringBuilder result = new StringBuilder();
        long start = System.currentTimeMillis();

        for(int i = 0 ; i < 100000; i++){
            result.append("test");
            result.substring(0, 1);
        }
        long end = System.currentTimeMillis();

        System.out.println(" String builder exec time : " + (end - start));

    }
```

3밀리 세컨드의 시간이 걸렸습니다. 차이가 눈에 띄게 많이 납니다. 즉 이러한 반복적인 문자열 연산에서는 `String` 보다는 `StringBuilder` 또는 `StringBuffer`를 사용해야 합니다. String 객체를 계속 생성할경우 heap 메모리에 GC의 대상이 되는 객체가 늘어나게 되고 성능에 영향을 끼칠수 밖에 없어집니다.

```bash
String builder exec time : 3
```

`StringBuffer` 나 `StringBuilder` 는 mutable한 객체로 내부를 살펴 보면 `String` 과 달리 char array 가 final 키워드 없이 선언된것을 확인 할 수 있습니다.

> char[] value;

`StringBuffer` 의 `StringBuilder` 차이는 동기화에 대한 차이로 `Thread-safe` 유무의 차이인데 이번 포스팅에서는 다루지는 않겠습니다.

왜 `String` class 는 immutable 하게 설계되었는지를 알아보겠습니다

* Security : network, db, user 정보등의 문자열을 사용함에 있어 mutable 하다면 보안상의 이슈가 발생할것 입니다.
* Synchronization and concurrency: 'immutable`하게 설계함으로 자동으로 동기화에 대한 이슈를 해결할 수 있습니다.
* Caching: String constatns pool 을 사용해 캐시 함으로 heap memory 자원에 대한 낭비를 막을 수 있습니다.

마지막으로, 이제 아래와 같은 코드에 대한 내부 동작을 명확하게 설명할 수 있습니다. 
`immutable`, `mutable`에 대한 차이 입니다.

```java
    StringBuilder b1 = new StringBuilder("test");
    StringBuilder b2 = b1;
    b2.append(" b2");
    b1.append(" b1");

    System.out.println(b1); // test b2 b1
    System.out.println(b2); // test b2 b1

    String s1 = "s1";
    String s2 = s1;
    s2 += "a";

    System.out.println(s1); // s1
    System.out.println(s2); // s1a
```

## 정리
문자열을 많이 다루어 보았으나 실제 내부 동작 원리에 대해서는 이해하지 못하고 있었습니다.  
간단히 나마 java에서 문자열을 다루는 방식에대해서 알수 있었고 시간이 된다면 StringBuilder, StringBuffer 에 대해서도 심도있게 다루어 보고 싶습니다.

---
## 출처
* [https://stackoverflow.com/questions/4648607/stringbuilder-stringbuffer-vs-operator](https://stackoverflow.com/questions/4648607/stringbuilder-stringbuffer-vs-operator)
* [https://study.com/academy/lesson/java-string-constant-pool-concept-mechanism.html](https://study.com/academy/lesson/java-string-constant-pool-concept-mechanism.html)
* [https://www.baeldung.com/java-string-immutable](https://www.baeldung.com/java-string-immutable)
