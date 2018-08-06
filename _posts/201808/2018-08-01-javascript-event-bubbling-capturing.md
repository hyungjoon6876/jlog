---
layout: post
title: 'javascript 이벤트 버블링(bubbling), 캡쳐링(capturing)'
tags: [javascript]
---
javascript 에서 event flow 는 3가지 개념을 갖고 있습니다.

* Capture Phase
* Target Phase
* Bubbling Phase

![eventflow]({{ site.baseurl }}/assets/img/201808/eventflow.svg)

DOM 트리 구조에서 이벤트 흐름은 `Capture Phase`, `Target Phase`, `Bubbling Phase` 순으로 이루어집니다.
이벤트 타겟에 이벤트가 정의되면 이벤트 흐름 순서가 먼저 결정됩니다. 이벤트 흐름은 설정 여부에 따라 생략 되기도 합니다.

`Capture Phase`는  이벤트 타겟의 최상위 개체인 `Window` 부터 전파를 시작합니다.  
`Window`, `Documnet`, `html`, `body` 순으로 개체가 전파되며 타겟 까지 전파됩니다.  
전파하는 과정에서 모든 Event listener가 트리거 됩니다.

`Bubbling Phase` 는 `Capture Phase` 와 반대로 `Window` 개체 까지 전파를 합니다.
`body`, `html`, `Document`, `Window`순으로 전파를 하며 마찬가지로 전파하는 과정의 모든 Event listener 가 트리거 됩니다.

## 버블링

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>event sample</title>
</head>
<body>
    <span>
        <p>영화 검색 결과</p>
        <div>
            <ul class="movie-list">
                <li><a href="#">어벤저스 - 인피니티 워</a></li>
                <li><a href="#">앤트맨 &amp; 와스프</a></li>
                <li><a href="#">미션 임파서블:폴아웃</a></li>
                <li><a href="#">인크레더블 2</a></li>
            </ul>
        </div>
    </span>
</body>    
</html>
```

예제에서는 이러한 DOM 트리 구조를 갖고있습니다. `span`, `div`, `a` 태그에 click 이벤트를 추가해보겠습니다.

```html
<html>
    <body>
        <span>
            <div>
                <ul>
                    <li><a></a></li>
                </ul>
            </div>
        </span>
    </body>
</html>
```

```javascript

    document.querySelector('span')
            .addEventListener('click', (e) => {
                console.log('span click ', e.eventPhase);
            });

    document.querySelector('div')
            .addEventListener('click', (e) => {
                console.log('div click ', e.eventPhase);
            });
    
    document.querySelectorAll('ul.movie-list li a')
            .forEach(ele => {
                ele.addEventListener('click', (e) =>{
                    console.log('a click ', e.currentTarget.innerHTML);
                    console.log('a click ', e.eventPhase);
                });
            });
```

`<a>` 클릭시 얻는 결과 입니다.  `<a>`의 이벤트가 상위 개체인 span 까지 전파됬음을 알수있습니다. 
```bash
a click  미션 임파서블:폴아웃
a click  2
div click  3
span click  3
```
`eventPhase`는 현재 이벤트 흐름의 실행단계를 보여주는 상수입니다.  
 * 1 : capturing phase
 * 2 : target phase
 * 3 : bubbling phase

> target.addEventListener(type, listener[, useCapture]);

javascript `addEventListener` function 의 default는 bubbling 만 하도록 되어있습니다. 3번째 파라미터에 옵션값을 주지 않으면 `bubbling` 전파만을 캐치 합니다.
3번째 파라미터에 useCapture 값을 true로 넣어주면 캡쳐링 이벤트를 캐치 합니다.

## 캡쳐링
예제의 addEventListener 3번째 파라미터에 true 값을 넣어보겠습니다.
```javascript

    document.querySelector('span')
            .addEventListener('click', (e) => {
                console.log('span click ', e.eventPhase);
            }, true);

    document.querySelector('div')
            .addEventListener('click', (e) => {
                console.log('div click ', e.eventPhase);
            }, true);
    
    document.querySelectorAll('ul.movie-list li a')
            .forEach(ele => {
                ele.addEventListener('click', (e) =>{
                    console.log('a click ', e.currentTarget.innerHTML);
                    console.log('a click ', e.eventPhase);
                });
            }, true);
```

캡쳐링 이벤트를 캐치해 상위 개체부터 로그를 출력합니다.

```bash
span click  3
div click  3
a click  미션 임파서블:폴아웃
a click  2
```

중간 `div`이벤트의 `useCaputer` parameter 만 true로 바꾸고 나머지 이벤트는 false로 바꾸어  `<a>` 이벤트를 발생시켜 테스트 해보겠습니다.
```javascript

    document.querySelector('span')
            .addEventListener('click', (e) => {
                console.log('span click ', e.eventPhase);
            }, false);

    document.querySelector('div')
            .addEventListener('click', (e) => {
                console.log('div click ', e.eventPhase);
            }, true);
    
    document.querySelectorAll('ul.movie-list li a')
            .forEach(ele => {
                ele.addEventListener('click', (e) =>{
                    console.log('a click ', e.currentTarget.innerHTML);
                    console.log('a click ', e.eventPhase);
                }, false);
```

`div` click 이벤트의 캡쳐링을 캐치하므로 div log가 먼저 출력됩니다.

```bash
div click  1
a click  미션 임파서블:폴아웃
a click  2
span click  3
```

좀더 이해하기 쉽게 `div` 클릭시 `bubbling` 을 캐치하는 이벤트를 추가해 테스트해보겠습니다.
`div` 클릭시 `capturing` `bubbling` 을 모두 캐치합니다.

```javascript
    document.querySelector('span')
            .addEventListener('click', (e) => {
                console.log('span click ', e.eventPhase);
            }, false);

    document.querySelector('div')
            .addEventListener('click', (e) => {
                console.log('div click (capturing)', e.eventPhase);
            }, true);

    document.querySelector('div')
                .addEventListener('click', (e) => {
                    console.log('div click (bubbling) ', e.eventPhase);
                }, false);
    
    document.querySelectorAll('ul.movie-list li a')
            .forEach(ele => {
                ele.addEventListener('click', (e) =>{
                    console.log('a click ', e.currentTarget.innerHTML);
                    console.log('a click ', e.eventPhase);
                }, false);
```

아래와 같이 로그를 출력합니다.  앞서 언급한 `Caputre Phase`, `Target Phase`, `Bubbling Phase`순으로 이벤트 흐름이 전파하는 것입니다.

```bash
div click (capturing) 1
a click  앤트맨 &amp; 와스프
a click 2
div click(bubbling)  3
span click  3
```

## 이벤트 흐름 전파 방지
`Event.stopPropagation()`을 통해 흐름이 전파하는것을 막을수 있습니다.  
예제에서 `div` `capturing`이벤트 캐치 부분에 stopPropagation() 을 추가해보겠습니다.

```javascript
    document.querySelector('span')
            .addEventListener('click', (e) => {
                console.log('span click ', e.eventPhase);
            }, false);

    document.querySelector('div')
            .addEventListener('click', (e) => {
                e.stopPropagation();
                console.log('div click (capturing)', e.eventPhase);
            }, true);

    document.querySelector('div')
                .addEventListener('click', (e) => {
                    console.log('div click (bubbling) ', e.eventPhase);
                }, false);
    
    document.querySelectorAll('ul.movie-list li a')
            .forEach(ele => {
                ele.addEventListener('click', (e) =>{
                    console.log('a click ', e.currentTarget.innerHTML);
                    console.log('a click ', e.eventPhase);
                }, false);
```

이벤트가 전파되지 않음을 알수있습니다.

```bash
div click (capturing) 1
```

## 정리
front 페이지 개발을 지속적으로 경험해 왔는데 이러한 정확한 이벤트 흐름에 대해서는 최근에 알게되었습니다.
그동안 개념을 모르고 개발해왔던 것이 부끄러웠습니다.   
이러한 이벤트 전파 방식을 잘 활용한다면 좀더 유연한 javascript coding 이 가능할것 같습니다.
---
## 출처
* [https://medium.com/@vsvaibhav2016/event-bubbling-and-event-capturing-in-javascript-6ff38bec30e](https://medium.com/@vsvaibhav2016/event-bubbling-and-event-capturing-in-javascript-6ff38bec30e)
* [https://developer.mozilla.org/ko/docs/Web/API/Event](https://developer.mozilla.org/ko/docs/Web/API/Event)
