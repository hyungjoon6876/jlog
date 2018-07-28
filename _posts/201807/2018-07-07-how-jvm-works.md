---
layout: post
title: 'JVM GC 에 대한 이해'
tags: [Java]
---
# JVM 구조 동작원리 그리고 GC
Java 프로그램은 JVM 위에서 동작하고 관리됩니다.
JVM 은 플랫폼에 종속적이지 않으며 메모리 관리도 알아서 해준다고 하는데 과연그럴까?
JVM이 구체적으로 어떤 구조를 갖고있고 어떻게 동작하며 효과적인 GC를 위해 개발자가 알아야 할 주의 사항에 대해 알아보겠습니다.

## JVM?
Java Compiler 가 `java`파일을 `class` java byte code로 컴파일을 하는데 이 `class` 파일을 OS가 해석할수 있도록 
도와주는 역할을 합니다.

### JVM 구조

![jvm struct]({{ site.baseurl }}/assets/img/20180707/architecture-of-jvm.jpg)  

 * Class Loader
 * Runtime Data Areas
 * Excution Engine

JVM 은 크게 3영역으로 나눕니다.

#### Class Loader
Runtime 시에 `class` byte code를 로딩해줍니다.
클래스의 인스턴스를 생성하면 클래스 로더를 통해 메모리에 로드하게 됩니다.
 * 부트스트랩 클래스 로더
 * 확장 클래스 로더
 * 시스템 클래스 로더
 클래스 로더에도 3가지 종류가 있는데 불러지는 자바 라이브러리가 제가각 다릅니다.

#### Runtime Data Areas

JVM이 OS로 부터 프로그램 실행을 위해 할당받은 메모리 영역

 * PC Registers
 * JVM Stacks
 * Native MEthod Stacks
 * Method Area
 * Heap

Runtime Data Areas 는 5개의 영역으로 나눌수 있습니다.  
PC Registers, JVM Stacks, Native Method Stacks는 Thread 별 생성이 되고
Method Area, Heap 영역은 모든 Thread 공유가 가능합니다.

##### PC Registers
현재 실행되고있는 JVM instruction 의 주소를 갖고있습니다.

##### JVM Stacks
Thread 별로 Method가 호출될떄 메모리를 차지합니다.
정확이 말하면 JVM stack 에는 `stack frame` 형태의 정보가 저장이되는 것이고 `stack frame`은 새로운 method가 호출될때마다 생성이 됩니다.

##### Native Method Stacks
Java 이외의 이기종 언어에서 제공되는 Method 정보가 저장되는 공간입니다.

##### Method Area
Class Loader가 읽어드린 `class` byte code 가 저장되는 영역입니다.
`new` 연산을 통해 객체가 생성되고 method가 실행되면 클래스 코드에 대한 정보를 저장하게 됩니다.
 * Type Information
 * Field Information
 * Method Information
 * Class Variable

4가지 타입으로 구분되어 저장됩니다.

##### Heap

* Permenant Generation 
* Young Generation
* Old Generation

> `Java8`에서는 Permenant Generation 이 없어지고 `metaspace`영역이 새로 생겼습니다.  
> `metaspace`은 Heap 영역의 일부는 아닙니다.

![Java8 jvm heap]({{ site.baseurl }}/assets/img/20180707/Java8-heap.jpg)

새로운 객체가 생성될 떄마다 그 객채에 대한 주소값이 저장됩니다.
모든 Thread에서 참조와 접근이 가능합니다.
GC의 대상이 되는 영역입니다.

#### Excution Engine
클래스 로더에 의해 JVM에 올려진 바이트 코드를 기계어로 변경하고 실행하는 역할을 합니다.
 * Interpreter
 * JIT (Just-In-Time) compiler

JIT compiler 는 Interpreter 의 단점을 개선하기 위한 방식으로 프로그램이 실행될때 바이트 코드를 기걔어로 변경합니다.
 

## GC
동적으로 할돵된 메모리 영역 중에서 사용할수 없는 영역을 탐지해 해제하는 기능입니다.
기본적으로 최초의 객체 참조(root set)로 부터 `reachable`,`unreachable` 상태 따라 Garbage Collector 가  GC 여부를 판단합니다.  
크게 `Young Generation` 와 `Old Generation`에서의 동작으로 나누어 집니다.  
다양한 Garbage Collector 방식이 존재합니다. 

* Serial GC
* Parallel GC
* Parallel Old GC(Parallel Compacting GC)
* Concurrent Mark & Sweep GC(CMS)
* G1(Garbage First) GC

G1 GC는 Young/Old Generation 에 대한 구분없이 동작합니다.
> G1 GC 는 Java8d의 기본 GC 입니다.

### Young Generation
 * Minor GC 발생
 * Major GC 에 비해 속도 빠름

새로운 객체가 생성되면 Young Generation 영역에 생성됩니다.
이영역에서 객체가 사라질때 Minor GC 가 발생합니다.

### Old Generation
 * Major GC 발생
 * Minor GC 에 비해 속도 느림

 Minor GC 에서 살아남은 객체들이 Old Generation으로 이동합니다.  
 Old Generation 영역이 모두 사용되면 Major GC가 일어납니다.



### GC의 중요성
GC가 메모리를 복사하고 해제하는 작업을 위해서는 GC 작업 Thread 이외의 Thread는 모두 작업을 멈춥니다.  
`stop the world`  
프로그램 장애로 이어질수 있기때문에 서비스의 안정성을 위해서는 반드시 고려 해야 합니다.

추가적으로 GC 방법에 따라 4가지 방식의 reference type이 존재합니다.  
GC 메카니즘에서 메모리에대한 회수는 메모리 참조 뿐 아니라 레퍼런스 타입 유형에 따라 의존하기 때문에 알아 두는것이 좋을것 같습니다.

## 정리
성능 좋은 Java 프로그램 개발을 위해서 JVM 구조와 원리, GC 메카니즘에 대하여 정확히 파악하고 있는것이 중요해 보입니다.  
GC는 알아갈수록 어려운것 같습니다. 다양한 GC 방식에 대하여 좀더 공부하고 포스팅할 예정입니다.

---
### 출처
 * [https://d2.naver.com/helloworld/37111](https://d2.naver.com/helloworld/37111)
 * [https://dzone.com/articles/java-8-permgen-metaspace](https://dzone.com/articles/java-8-permgen-metaspace)
 * [http://karunsubramanian.com/wp-content/uploads/2014/07/Java8-heap.jpg](http://karunsubramanian.com/wp-content/uploads/2014/07/Java8-heap.jpg)
 * [https://www.studytonight.com/java/images/architecture-of-jvm.jpg](https://www.studytonight.com/java/images/architecture-of-jvm.jpg)