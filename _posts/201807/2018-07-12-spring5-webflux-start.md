---
layout: post
title: 'Spring framework 5 webflux 시작하기'
tags: [Java, Spring framework, reactive programming]
---

 `Spring framework5` 에서 `web-flux`모듈이 새롭게 추가되었습니다.  
 `web-flux`는 server, client side 에서 reactive 스타일의 application 개발을 도와주는 모듈입니다.

## web-flux
server side 에서 web-flux 는 2가지 방식으로 나누어집니다.
* 어노테이션 기반
* 함수형 프로그래밍 기반

![web-flux]({{ site.baseurl }}/assets/img/20180712/webflux-overview.png)  

`web-flux`모듈의 모델 입니다.  
두방식 모두 reactive stream api, non blocking 방식으로 실행됩니다.

### annotiation base model
어노테이션 기반은 기존에 `@RestController` 어노테이션을 이용해 개발한 방식입니다.

```java

@RestController
public class BabyController {

	@GetMapping("/baby/{name}")
	Mono<Baby> baby(@PathVariable String name) {
		return Mono.just("mj");
	}

	@GetMapping("/baby")
	Flux<Baby> babyList() {
		return Flux.just("mj", "kim", "hj", "yuri");
	}
}

```
눈에 보이는 기존 방식과 다른점은 `@GetMapping` 어노테이션 입니다.  
이름에서 유추할수 있듯이 `@PostMapping`, `@PutMapping`, `@DeleteMapping` 등이 존재합니다.  
`Handler mapping`과 `Handler adaptor`는 non blocking이며 `HttpServletRequest` 와 `HttpServletResponse` 객체 대신에 `ServerHttpRequest` 와 `ServerHttpResponse`객체로 동작을 합니다.


### functional programming model
functional programming model은 `HandlerFunction` 과 `RouterFunction` 나누어 구현합니다.
* HandlerFunction - `http` 요청을 `ServerRequest` 객체를 가져와  Mono<ServerResponse> 형태로 return 합니다.
* RouterFunction - `http` 요청에 대해서 `HandlerFunctoin` 에 routing 해줍니다. Mono<HandlerFunction> 형태로 return

### HandlerFunction

```java
@Component
class BabyHandler {

    public Mono<ServerResponse> baby(ServerRequest request) {

        Baby baby = Stream.of(new Baby("mj", 1), new Baby("sw", 1), new Baby("yr", 33))
                .filter(s -> s.getName().equals(request.pathVariable("name")))
                .findFirst()
                .orElse(new Baby("", 0));

        return ServerResponse.ok()
                .contentType(APPLICATION_JSON)
                .body(Mono.just(baby), Baby.class);
    }

    public Mono<ServerResponse> babyList(ServerRequest request) {

        Flux<Baby> babyFlux = Flux.just(new Baby("mj", 1), new Baby("sw", 1), new Baby("yr", 33));

        return ServerResponse.ok()
                .contentType(APPLICATION_JSON)
                .body(babyFlux, Baby.class);
    }

    public Mono<ServerResponse> post(ServerRequest request) {

        Mono<Baby> mono = request.bodyToMono(Baby.class);

        return ServerResponse.ok()
                .contentType(APPLICATION_JSON)
                .body(mono, Baby.class);

    }

 }
```

HandlerFunction 예제입니다.  
`Baby` 객체를 생성 조회 하는 기능을 갖고있는 `HandlerFunction`입니다.

> Mono\<ServerResponse> post(ServerRequest request)

Handler method 들의 parameter 와 return 형태는 `ServerRequest`, `Mono<ServerResponse>` 형태입니다.  
앞서 `web-fulx`에서는 `HttpServletRequest`, `HttpServletResponse`대신에 `ServerHttpRequest`, `ServerHttpResponse`객체를 사용한다고 하였습니다. 이 말은 `web-flux` 에서는 servlet-api 를 활용할수 없다는 의미입니다.

`ServerRequest` 객체의 method들 중 `bodyToMono`, `bodyToFlux`가 생소해 보입니다.

>  Mono\<T\> bodyToMono(Class<? extends T> var1)  
>  Flux\<T\> bodyToFlux(Class<? extends T> var1)

* Mono - element가 1개
* Flux - element가 0개 또는 다수개

### RouterFunction
```java
@Configuration
class BabyRouter {

    @Bean
    RouterFunction<?> routes(BabyHandler bh) {

        return RouterFunctions.route(GET("/baby").and(accept(APPLICATION_JSON)), bh::babyList)
                .andRoute(GET("/baby/{name}").and(accept(APPLICATION_JSON)), bh::baby)
				//.andRoute(POST("/baby"), serverRequest -> bh.createBaby(serverRequest));
                .andRoute(POST("/baby").and(accept(APPLICATION_JSON)), bh::createBaby)
                
    }
}
```

`RouterFunctions` 객체의 `route`, `andRoute`메소드로 http 요청에 따른 HandlerFunction 을 매핑시켰습니다.    
java8 `method-reference` 를 활용해서 코드를 더 간결하게 포현할수 있습니다.

> \<T extends ServerResponse> RouterFunction<T> route(RequestPredicate predicate, HandlerFunction<T> handlerFunction)  

`RequestPredicate`는 functional interface로 `RequestPredicates` class의 static method로 http method, url, content/type 등을 정의 하였습니다.

## 정리
간단히 spring5에서 제공한 `web-flux` 사용범을 정리해 보았습니다.  
`HandlerFunction` 과 `RouterFunctaion` 기반한 프로그래밍 방법은 기존 어노테이션 방법과 사용법이 확 달라져 생소하였으나 금방 익숙해질것으로 보여집니다.  
또한 `functional programming`, `reactive programming`, `rx`, `reactor` 에 대한 개념과 정리들을 통해 좀더 `web-flux` 를 제대로 활용할수 있도록 해보겠습니다.

---
## 출처
* [https://docs.spring.io/spring/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html](https://docs.spring.io/spring/docs/5.0.0.BUILD-SNAPSHOT/spring-framework-reference/html/web-reactive.html)
* [https://github.com/joshlong/flux-flix-service](https://github.com/joshlong/flux-flix-service)
