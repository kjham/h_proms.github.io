---
layout: post
title: 'RxJS - Daum영화에 적용하다'
author: john.stonesoup
date: 2017-01-09 14:00
tags: [rxjs, functional-programming, reactive-programming]
---

2016년 개편된 [Daum영화] 프론트엔드 개발에는 [ReactiveX]의 JavaScript 라이브러리 [RxJS 4]를 사용했습니다.  
[ReactiveX]는 [ReactiveX Introduction]에서 callback의 문제점에 도움을 주는 것으로 소개가 되어 있습니다.  
웹에서는 [Angular 2]에 [RxJS 5]가 쓰이며 관심을 받았고, Android app개발에서도 [RxJava]가 여러 곳에 쓰이며 관심을 받고 있습니다.  
적용 당시 IE 브라우저 버전 대응 문제와 [RxJS 5]가 Beta 였던 이유로 [RxJS 4]를 적용했습니다.  
RxJS는 프론트엔드의 여러 이벤트들에 대한 비동기 처리에 도움을 주지만, 수많은 operator들과 함수형 프로그래밍 패러다임에 대한 이해를 해야 해서 어려움이 있었는데요. 예를 들어 함수형 프로그래밍 패러다임에 맞게 외부 변수를 참조하지 않고 적절한 함수의 합성을 생각해야 하는 점이었습니다.  
그렇다면 ReactiveX가 무엇인지 간략히 먼저 소개하고, RxJS를 Daum영화에는 어떻게 적용했는지 각 기능별 코드를 중심으로 소개하겠습니다.  
이해를 돕기 위해 코드를 간소화 한 점이 있으며, 실제 적용 코드와 큰 흐름에서는 같도록 했음을 알려드립니다.  

## ReactiveX
[http://reactivex.io/intro.html](http://reactivex.io/intro.html)

ReactiveX의 Introduction을 보면 여러 item들을 비동기로 받기 위한 방식이고 Observer pattern을 사용한다고 나와 있습니다. Single item의 비동기는 JavaScript에서는 [Promises](https://www.promisejs.org/)를 생각하실 수 있습니다.     
onNext, onError, onCompleted 의 3가지 callback이 소개되어 있어, error처리를 onError에서 한 번에 할 수 있다는 점과 onNext로 각각의 item을 받다가 정상적으로 끝나면 onCompleted가 호출되는 내용이 나와 있습니다.  
subscribe는 onError가 발생하든 onCompleted가 발생하든 끝나며, onError 발생 시 onCompleted는 호출되지 않습니다.  
![ReactiveX Observable (<a href="http://reactivex.io/documentation/observable.html">http://reactivex.io/documentation/observable.html</a>)에 소개된 marble diagram](http://reactivex.io/assets/operators/legend.png)

추가로 ReactiveX는 다양한 operator를 제공합니다. 잘 활용하기에 따라서 장점이 될 수도 있고, operator가 너무 많아서 진입장벽이 커지는 단점이 될 수 있다고 생각합니다.   
Rx의 operator들은 operators([http://reactivex.io/documentation/operators.html](http://reactivex.io/documentation/operators.html))에 자세히 소개되어 있으며, operator를 선택하면 자세한 설명이 marble diagram이라 불리는 다이어그램과 함께 나와 있습니다.  
Marble diagram은 Observable([http://reactivex.io/documentation/observable.html](http://reactivex.io/documentation/observable.html))에 소개되어 있고, Marble diagram 중심으로 operator가 소개된 사이트 RxMarbles([http://rxmarbles.com/](http://rxmarbles.com/))도 있습니다.  

## 서제스트 검색 자동완성
서제스트 검색 자동완성은 서제스트 검색창에 검색어를 입력하면 이를 서버에 보내 결과를 그려주는 기능입니다.  

![](/files/daummovie_mobile_suggest_example.png)

자동완성에 적절한 operator를 찾다가 [RxJS autocomplete example code]에서 사용된 operator들을 참고했고, 아래에는 참고한 operator들을 사용한 이유와 그 외 operator들을 사용한 이유를 소개합니다.  
먼저 검색 자동완성의 유효한 입력을 거르기 위해 서버에 요청하는 flatMapLatest 직전까지 사용한 operator들이 있습니다.  
keyup event가 발생할 때 입력된 텍스트 값을 map으로 넘겨주고 300ms미만의 간격의 키 입력을 제외하기 위해 300ms의 debounce를 주었습니다.  
특수키 등을 입력 시에는 같은 값이 중복 요청될 소지가 있어 distinctUntilChanged을 적용했습니다.  
scan을 이용하여 텍스트 박스에 default로 입력된 originalValue가 있을 시 이 값과 다른 값이 입력될 때까지 null을 return하여 그 아래 filter에서 걸러지게 하였습니다.   
이런 경우는 첫 입력이라 distinctUntilChanged 에서 걸리지 않기 때문입니다.  

```javascript
Rx.Observable.fromEvent(searchInputField, 'keyup') 
  .map(function (e) {return e.target.value;})      
  .debounce(300) 
  .distinctUntilChanged()      
  .scan(function(prev, current) { 
        if (prev == null && current == originalValue) {
            return null;          
        } 
        return current;      
  }, null)      
  .filter(function(text) {return text != null})      
```   

### 업데이트 된 내용  
원래 내용에서는 scan과 filter조합으로 distinctUntilChanged 이후에 첫 originalValue 걸러지게 하였는데, 복잡하여 다른 방법을 찾아 소개 합니다.  
startWith와 skip의 조합입니다. originalValue로 시작하여 distinctUntilChanged 이후에는 skip(1)을 하여 첫 originalValue 1개는 skip하도록 하는 것 입니다.  
이렇게 하면 default로 입력된 originalValue는 최초 distinctUntilChanged에 영향을 주게 되면서 skip할 수 있습니다.  

```javascript
Rx.Observable.fromEvent(searchInputField, 'keyup') 
  .map(function (e) {return e.target.value;})      
  .debounce(300)
  .startWith(originalValue) 
  .distinctUntilChanged()      
  .skip(1)   
```   

여기까지 찾은 유효한 입력은 flatMapLatest를 통해 server에 요청하게 됩니다.  
비동기 방식으로 결과가 오기 때문에, 서버로부터 응답이 오는 순서가 섞이면 flatMapLatest를 사용하여 요청에 대한 응답이 이미 온 응답보다 앞선 요청일 경우 무시하게 했습니다.  
현재는 mobile환경에서만 서제스트 검색에서 닫기 버튼이 있어 PC환경과 반응형 웹 대응으로 인해 takeUntil이 없지만, 두 환경에 닫기 버튼이 있을 당시에는 takeUntil을 적용하여 닫기 버튼을 누를 때까지 키보드 입력에 대한 event를 처리한 코드도 있어 함께 소개합니다.  

```javascript
Rx.Observable.fromEvent(searchInputField, 'keyup') 
  .map(function (e) {return e.target.value;})      
  .debounce(300) 
  .startWith(originalValue) 
  .distinctUntilChanged()      
  .skip(1)       
  .flatMapLatest(function(text) {          
    return Rx.Observable.fromPromise(
        $.ajax({ url: suggestUrl,
   	          dataType: "jsonp"  })); 
  })      
  .takeUntil(Rx.Observable.fromEvent(
      $("#search_close_btn"), 'click').take(1))
  .retry(3) 
  .subscribe(
    function onNext(result) {
        updateSuggestDom(result);
    },
    function onError(err) {
        suggestSearchOnError(err);
    },
    function onCompleted() {
        // NOTHING
    }
  );
```

이렇게 하면 닫기 버튼을 눌렀을 때 unsubscribe를 처리하는 쪽과 subscribe를 하는 쪽을 나눌 필요 없이, 한 코드에서 시작부터 끝까지 흐름을 볼 수 있습니다.  
마지막으로 error 발생 시 3번 retry하도록 했습니다. 혹시라도 일시적인 장애로 서버에 오류가 난다면 3번까지는 재시도하기 위함입니다.  

## 서제스트 검색 PC 방향키 적용
![](/files/daummovie_pc_suggest_example.png)  

유효한 입력이 발생하여 자동완성 결과로 적절하게 DOM을 update하면, PC환경에서 위/아래 방향키를 누르면 해당 방향으로 선택이 되고 enter입력 시 선택된 링크로 이동이 되도록 해야 합니다.  
scan을 이용해서 방향키나 엔터가 입력될 때마다 이전에 넘어온 위치 값을 받아서 적절한 위치로 선택이 이동되거나, enter를 입력 시 해당 위치로 이동이 되도록 구현한 코드입니다.  

```javascript
Rx.Observable.fromEvent(window, 'keydown') 
  .map(function (e) {return e.keyCode;}) 
  .filter(function (code) {
    return code == 38 || code == 40 || code == 13 })
    // up(38), down(40), enter(13)   
  .scan(function(prevKeyAndIndexObj, currentKeyCode) { 
        var indexGap = 0;         
        switch(currentKeyCode) { 
            case 38:
                indexGap = -1;                         
                break;
            case 40:
                indexGap = 1;                 
                break;
            case 13:
            default: 
                indexGap = 0;
                break;         
        }
        var prevFocusedIndex = prevKeyAndIndexObj.focusedIndex;         
        return {             
          focusedIndex: Math.min(Math.max(indexGap + prevFocusedIndex, -1),
            suggestList.length - 1),
          keyCodeValue: currentKeyCode         
        };     
  }, {focusedIndex: -1, keyCodeValue: -1})    
  .subscribe(
      function onNext(keyAndIndexObj) {
            var focusedIndex = keyAndIndexObj.focusedIndex;
            var keyCodeValue = keyAndIndexObj.keyCodeValue;
            // arrow key (up / down)
            if (keyCodeValue == 38 || keyCodeValue == 40) {
                updatePositionToFocusedIndex(focusedIndex);
            }
            // enter key when focused
            else if (keyCodeValue == 13 && focusedIndex >= 0) {
                location.href = getUrlFromIndex(focusedIndex);
            }
      },
      function onError(err) {
          suggestSearchOnError(err);
      },
      function onCompleted() {
          // NOTHING
      }
  );      
```   

## 평점 늘리기  

개편 프로젝트에 새로 들어간 기능입니다.  
사이드 메뉴 프로필을 통해 들어가면 본인이 평점을 주지 않은 영화들의 목록이 나와 평점과 리뷰를 남길 수 있게 하는 '평점 늘리기' 기능이 있습니다.  
![](/files/daummovie_expanding_evaluation_example.png)

적용된 shareReplay operator는 parameter로 넘겨준 숫자만큼의 item들은 최초 subscribe에서만 처리하여 가지고 있다가, 이후의 subscribe에서는 최초에 가지고 있던 item들을 replay하기 위한 operator입니다.  
평점 늘리기에 보여줄 전체 movieId들을 미리 한 번만 받아놓고, 이후 다른 operator들을 붙여 subscribe하게 될 때 불필요하게 모든 id들을 다시 받아오는 일이 없도록 하기 위함입니다.  
이렇게 하면 여러 id들을 저장할 array를 준비하고 그 array로 가져올 값을 요청하되 중복 요청했는지 체크하는 로직을 별도로 구현할 필요 없이, Rx에서 제공하는 operator들로 한 곳에서 처리할 수 있는 장점이 있습니다.   

> shareReplay에 대해서 자세히 알고 싶으시다면 배경지식으로 Hot Observables, Cold Observables, Hot Observables replied에 대해 설명된<br>  <a href="https://github.com/Reactive-Extensions/RxJS/blob/master/doc/gettingstarted/creating.md">https://github.com/Reactive-Extensions/RxJS/blob/master/doc/gettingstarted/creating.md</a>를 참고하세요.

```javascript
var bulkMovies = Rx.Observable.defer(function() {
  return Rx.Observable.fromPromise(
    $.ajax({url: 'get_movie_ids.json'}));
}).shareReplay(1);
```

shareReplay 이후의 operator들에 대한 더 자세한 설명을 위해 이어서 아래의 코드를 예제로 보실 수 있습니다.  
아래의 코드는 더보기를 눌렀을 때 현재 위치의 offset을 받아, 그 offset으로 부터 pageSize만큼 영화 정보(포스터, 제목 등등)를 받아와 그려주는 코드입니다.  

```javascript
bulkMovies.flatMap(function(result) { 
  var data = result.data; 
  if (data.size == 0) { 
    alert('Data가 없습니다.');
    return Rx.Observable.empty();
  } 
  var pageMovies = data.movies.slice(offset, offset+pageSize).join("/"); 
  return Rx.Observable.fromPromise( $.ajax({url:      
    'get_detail_movies_by_ids.json?ids=' + pageMovies}));
 }).subscribe(
  function onNext(result) {
      appendNewMovies(result);
  },
  function onError(err) {
      // NOTHING
  },
  function onCompleted() {
      // NOTHING
  });
```

문뜩 위 코드가 flatMap함수 안에서 if문으로 분기하고 data가 없을 때 flatMap에서 끝내는 방식이 함수형 프로그래밍이나 Rx의 개념과 맞지 않는 듯하여, 아래와 같이 바꿔볼 수도 있다는 생각이 듭니다.  
아래와 같이 [if operator](https://github.com/Reactive-Extensions/RxJS/blob/master/doc/api/core/operators/if.md)와 [throw operator](https://github.com/Reactive-Extensions/RxJS/blob/master/doc/api/core/operators/throw.md)의 조합으로 data가 없을 시 onError에서 처리하는 방법도 남겨 둡니다.  

```javascript
bulkMovies.flatMap(function(result) { 
  var data = result.data; 
  return Rx.Observable.if(
    function() {
      return data.size == 0;
    },
    Rx.Observable.throw(new Error('Data가 없습니다.')),
    Rx.Observable.defer(function() {
        var pageMovies = data.movies.slice(offset, offset+pageSize).join("/"); 
        return Rx.Observable.fromPromise($.ajax({url:      
            'get_detail_movies_by_ids.json?ids=' + pageMovies}));
    })
  );
 }).subscribe(
  function onNext(result) {
      appendNewMovies(result);
  },
  function onError(err) {
      alert(err.message);
  },
  function onCompleted() {
      // NOTHING
  });  
```

## 그 밖에...

적용과 별개로 RxJS에서 알게 된 그 밖의 내용을 소개해드리고자 합니다.  
[RxJS 4 Read Me]에 따르면 RxJS = Observables + Operators + Schedulers로 소개되어 있습니다.  
본 사례에서는 Observable과 Operator만 소개되었지만, item을 처리하는 순서를 이해하고 디테일하게 컨트롤하기 위해서는 scheduler에 대한 이해가 필요합니다.  
[Rx.Scheduler class] 에는 scheduler와 scheduling에 대한 자세한 설명이 소개되어 있습니다.  
크게 3가지 scheduler가 있는데, 즉시 처리하는 immediate, 내부적으로 queue에 작업을 넣어 trampoline을 이용하는 currentThread, 비동기 방식의 default가 있습니다.  
사용할 scheduler를 바꾸고자 할 때는 [subscribeOn] 과 [observeOn] operator를 적절히 사용하시면 됩니다.  
Virtual DOM과 연계하여 RxJS가 필요하다면 [Cycle.js]나, React redux 환경에서는 [redux-observable]을 함께 고려하실 수 있을 것 같습니다.    

## 마치며...

여러 곳에서 발생하는 이벤트, AJAX 호출로 인해 callback이 많아져 callback hell이 생기면서 이벤트의 흐름을 찾기 어려워지는 경우가 있습니다.  
여기서 적용사례의 모든 것을 다 다루진 못했지만, RxJS는 여러 AJAX 비동기 응답을 한 곳에 모아야 하는 경우나 어떤 AJAX의 응답이 다른 AJAX의 input값이 되는 경우에도 유용했습니다.  
그러나 어려운 점으로는 함수형 프로그래밍보다 명령형 프로그래밍에 더 익숙한 개발자로서 이벤트의 흐름을 함수의 합성으로 생각해야 하고, 많은 operator들 중 적합한 operator를 알고 있지 않으면 찾아봐야 하는 점이었습니다.  
RxJS를 Daum영화에 적용한 코드가 Rx,함수형 프로그래밍 관점에서 볼 때 아직 부족한 부분이 있을 수 있겠으나, callback hell로 인한 어려움에 대응할 수 있음을 확인한 사례였습니다.  


[ReactiveX]: http://reactivex.io/
[ReactiveX Introduction]: http://reactivex.io/intro.html
[RxJava]: https://github.com/ReactiveX/RxJava
[Daum영화]: http://movie.daum.net
[RxJS 4]: https://github.com/Reactive-Extensions/RxJS
[RxJS 5]: https://github.com/ReactiveX/rxjs
[Angular 2]: https://angular.io/
[RxJS 4 Read Me]: https://github.com/Reactive-Extensions/RxJS/blob/master/readme.md  
[RxJS autocomplete example code]: https://github.com/Reactive-Extensions/RxJS/blob/master/examples/autocomplete/autocomplete.js
[Rx.Scheduler class]: https://github.com/Reactive-Extensions/RxJS/blob/master/doc/api/schedulers/scheduler.md
[subscribeOn]: https://github.com/Reactive-Extensions/RxJS/blob/master/doc/api/core/operators/subscribeon.md
[observeOn]: https://github.com/Reactive-Extensions/RxJS/blob/master/doc/api/core/operators/observeon.md
[Cycle.js]: https://cycle.js.org/
[redux-observable]: https://github.com/redux-observable/redux-observable
