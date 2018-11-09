---
layout: post
title: 'hello gradle world'
tags: [gradle]
---

gradle 은 오픈소스 빌드 자동화 도구입니다. build 스크립트는 groovy 나 kotlin 언어로 작성 할 수 있습니다.

## install
OS 에 따라 다양한 방법으로 설치 가능합니다.
`homebrew` , `scoop` 과 같은 패키지 매니저를 통해 설치할 수 있고 [바이너리 파일]](https://gradle.org/releases/)을 받아 설치할 수 있는 방법도 있습니다.

설치 한 후 확인해 보았습니다.
``` bash
gradle -v
------------------------------------------------------------
Gradle 4.10.2
------------------------------------------------------------

Build time:   2018-09-19 18:10:15 UTC
Revision:     b4d8d5d170bb4ba516e88d7fe5647e2323d791dd

Kotlin DSL:   1.0-rc-6
Kotlin:       1.2.61
Groovy:       2.4.15
Ant:          Apache Ant(TM) version 1.9.11 compiled on March 23 2018
JVM:          1.8.0-adoptopenjdk (Oracle Corporation 25.71-b00)
OS:           Windows 10 10.0 amd64

```


## gradle wrapper
`gradle wrapper` 는 선언된 버전의 gradle을 호출하고 필요하다면 다운로드하는 스크립트입니다.

gradle wrapper 를 먼저 추가 해야합니다.
`gradle wrapper` 커맨드로 추가합니다.

```bash
$ gradle wrapper

BUILD SUCCESSFUL in 0s
1 actionable task: 1 executed

```
gradle 프로젝트는 일반적으로 아래와 같은 구조를 갖습니다.
`gradlew`, `gradlew.bat` 는 window 환경에서의 wrapper build를 실행하기 위한 batch 스크립트입니다.

``` bash
.
├── build.gradle
├── settings.gradle
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
└── gradlew.bat
```

## gradle init
`init` 명령어를 통해 gradle build 환경을 초기화 할 수 있습니다.  
`--type`, `--test-framework` 옵션으로 plugin 과 test-framework 등을 설정 가능합니다.

* --type
    * java-application
    * java-library
    * scala-library
    * groovy-library
    * groovy-application
    * basic
* --test-fraemework
    * junit
    * spock
    * testng

``` bash
$ gradle init --type java-application --test-freamework spock

$ gradle init --type java-library --test-framework testng
```

간단히 현재시간과 properties 설정을 출력하는 task를 만들어 보겠습니다.

```groovy
description = 'global properties'
task printing(group : 'logging', description: 'println current date'){
    println project.properties.description
    println properties.description + ' : ' +  new Date().format('yyyyMMdd') 
}
```

wrapper 를 이용해 printing task 를 실행할수 있습니다.
``` bash
$ ./gradlew printing

> Configure project :
global properties
println current date : 20181107
```

`properties` 명령어를 통해 프로젝트의 attribute 를 확인 할 수 있습니다.

``` bash

$ gradle properties

```

---
## 출처
* [https://docs.gradle.org/current/userguide/userguide.html](https://docs.gradle.org/current/userguide/userguide.html)