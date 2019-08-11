---
layout: post
title: 'Vue.js 시작하기 - 2'
author: kjham.ham
date: 2019-07-24 13:00
tags: [swtech,java,vue-js,front-end]
image: assets/images/VUE_JS.jpg
---

# 인스턴스 & 컴포넌트

# 3. 인스턴스
인스턴스는 뷰로 개발할 때 필수로 생성해야하는 코드입니다.

## 3-1. 인스턴스 소개
아래 코드에서 Vue로 인스턴스를 생성하는 것을 보여줍니다.
~~~js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <!-- ... -->
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
    	// Vue를 사용하기 위해 필수로 생성하는 객체
        var vm = new Vue({
            el: '#app',
            data: {
                mesage: 'hi'
            }
        });
    </script>
</body>
</html>
~~~

## 3-2. 인스턴스와 생성자 함수
인스턴스와 생성자 함수를 사용하는 예는 다음과 같습니다.
~~~c
function Vue() {
	this.logText = function() {
		console.log('hello');
	}	
}
var vm = Vue();
vm.logText();
출력 -> hello
~~~

## 3-3. 인스턴스 옵션 속성
~~~c
new Vue({
	el: ,
	template: ,
	data: ,
	methods: ,
	created: ,
	watch: 
})
~~~
- el : 인스턴스가 그려지는 화면의 시작점 (특정 HTML 태그)
- template :  화면에 표시할 요소 (HTML, CSS 등)
- data : 뷰의 반응성(Reactivity)가 반영된 데이터 속성
- methods :  화면의 동작과 이벤트 로직을 제어하는 메서드
- created : 뷰의 라이프 사이클과 관련된 속성
- watch : data에서 정의한 속성이 변화했을 때 추가 동작을 수행할 수 있게 정의하는 속성

~~~js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <!-- ... -->
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var vm = new Vue({
            el: '#app',
            data: {
                mesage: 'hi'
            },
            methods: {

            },
            created: function() {
                
            }
        });
    </script>
</body>
</html>
~~~

# 4. 컴포넌트 소개
컴포넌트는 화면의 영역을 구분하여 개발할 수 있는 뷰의 기능입니다.  
컴포넌트 기반으로 화면을 개발하게 되면 코드의 재사용성이 올라가고 빠르게 화면을 제작할 수 있습니다.

## 4-1. 전역 컴포넌트
전역 컴포너트를 생성하면 모든 인스턴스에서 호출이 가능하지만, 실제로는 잘 사용하지 않습니다.
~~~js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <app-header></app-header>
        <app-content></app-content>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        // Vue.component('컴폰언트 이름', 컴포넌트 내용)
        // 전역 컴포넌트 (실제 프로젝트에서는 전역 컴포넌트는 거의 사용할 일 없음)
        Vue.component('app-header', {
            template: '<h1>Header</h1>'
        });
        Vue.component('app-content', {
            template: '<div>Content</div>'
        })

        new Vue({
            el: '#app'
        })
    </script>
</body>
</html>
~~~

## 4-2. 지역 컴포넌트
각 인스턴스 내 지역 컴포넌트를 사용하여 호출하는 방식을 살펴봅니다.
~~~js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <app-header></app-header>
        <app-content></app-content>
        <app-footer></app-footer>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        // Vue.component('컴폰언트 이름', 컴포넌트 내용)
        // 전역 컴포넌트 (실제 프로젝트에서는 전역 컴포넌트는 거의 사용할 일 없음)
        Vue.component('app-header', {
            template: '<h1>Header</h1>'
        });
        Vue.component('app-content', {
            template: '<div>Content</div>'
        })

        new Vue({
            el: '#app',
            // 지역 컴포넌트 등록 방식
            components: {
                // '컴포넌트 이름': 컴포넌트 내용
                'app-footer': {
                    template: '<footer>footer</footer>'
                }
            }
        })
    </script>
</body>
</html>
~~~

## 4-3. 컴포넌트와 인스턴스 관계
인스턴스를 추가로 더 생성하여 컴포넌트와의 관계를 살펴봅니다.
~~~js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <app-header></app-header>
        <app-footer></app-footer>
    </div>

    <div id="app2">
        <app-header></app-header>
        <!-- 지역 컴포넌트이기 떄문에 에러 발생 -->
        <app-footer></app-footer>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        // Vue.component('컴폰언트 이름', 컴포넌트 내용)
        // 전역 컴포넌트 (실제 프로젝트에서는 전역 컴포넌트는 거의 사용할 일 없음)
        Vue.component('app-header', {
            template: '<h1>Header</h1>'
        });

        new Vue({
            el: '#app',
            // 지역 컴포넌트 등록 방식
            components: {
                // '컴포넌트 이름': 컴포넌트 내용
                'app-footer': {
                    template: '<footer>footer</footer>'
                }
            }
        });

        // 대부분 서비스에서는 Vue 객체는 한 개만 생성하기는 함..
        new Vue({
            el: '#app2'
        })
    </script>
</body>
</html>
~~~

## 4.4. 컴포넌트 통신 : Props
뷰 컴포넌트는 각각 고유한 데이터 유효 범위를 갖습니다.  
따라서 컴포넌트 간에 데이터를 주고 받기 위한 규칙을 따라야 합니다.  

상위에서 하위로는 데이터를 내려줍니다. (프롭스 속성 : Props)  
하위에서 상위로는 이벤트를 올려줍니다. (이벤트 발생 : Event)  

위 관계가 없이 N방향 통신을 한다면, 데이터 통신 흐름이 굉장히 복잡해지고 버그 추적이 힘들어집니다.
~~~js
<!DOCTYPE html>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <!-- <app-header v-bind:프롭스 속성 이름="상위 컴포너트의 데이터 이름"></app-header> -->
        <app-header v-bind:propsdata="message"></app-header>
        <app-content v-bind:propsdata="number"></app-content>
    </div>
    
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var appHeader = {
            // 다음과 같이 props data 전달 가능
            template: '<h1>{{ propsdata }}</h1>',
            props: ['propsdata']
        }
        var appContent = {
        	// props명은 소문자로만 구성
        	// props명은 지역변수로 취급되어 appHeader의 props 이름과 동일해도 됨
            template: '<div>{{ propsdata }}</div>',
            props: ['propsdata']            
        }

        new Vue({
            el: '#app',
            components: {
                'app-header': appHeader,
                'app-content': appContent
            },
            data: {
                message: 'hi',
                number: 10
            }
        })
    </script>
</body>
</html>
~~~

## 4.5. Event Emit
Event Emit 을 어떻게 수행하는지 살펴봅니다.
~~~js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <!-- <app-header v-on:하위 컴포넌트에서 발생한 이벤트이름="상위 컴포넌트의 메소드 이름"></app-header> -->
        <app-header v-on:pass="logText"></app-header>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var appHeader = {
            template: '<button v-on:click="passEvent">click me</button>',
            methods: {
                passEvent: function() {
                    this.$emit('pass')
                }
            }
        }

        new Vue({
            el: '#app',
            components: {
                'app-header': appHeader
            },
            methods: {
                logText: function() {
                    console.log('hi');                    
                }
            }
        });
    </script>
</body>
</html>
~~~

## 4.6. Event Emit 실습
숫자를 가산하는 이벤트를 작성합니다.   
그리고 이벤트 발생 시, 산된 숫자를 표기하는 메소드를 호출하는 코드를 작성해봅시다.
~~~ html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <p>{{ num }}</p>
        <!-- <app-header v-on:하위 컴포넌트에서 발생한 이벤트이름="상위 컴포넌트의 메소드 이름"></app-header> -->
        <app-header v-on:pass="logText"></app-header>
        <app-content v-on:add="logNumber"></app-content>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var appHeader = {
            template: '<button v-on:click="passEvent">click me</button>',
            methods: {
                passEvent: function() {
                    this.$emit('pass')
                }
            }
        }
        var appContent = {
            template: '<button v-on:click="addNumber">add</button>',
            methods: {
                addNumber: function() {
                    this.$emit('add');
                }
            }
        }

        new Vue({
            el: '#app',
            components: {
                'app-header': appHeader,
                'app-content':  appContent
            },
            methods: {
                logText: function() {
                    console.log('hi');                    
                },
                logNumber: function() {
                    this.num += 1;
                    console.log(this.num);
                }
            },
            data: {
                num: 10
            }
        });
    </script>
</body>
</html>
~~~

## 4.7. 같은 컴포넌트 레벨 간의 통신 방법
Root 하위에 두 개(A, B)의 컴포넌트가 있을 때, 어떻게 데이터 통신을 할 수 있을까 ?  
B 에서 A 컴포넌트에 데이터를 전달한다고 가정할 때 다음과 같은 과정으로 수행할 수 있다.  
B 에서 Root 로 Event Emit을 이용하여 데이터를 전달한다.  
그리고 Root에서 A로 Props를 이용하여 데이터를 전달한다.  
~~~js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <app-header v-bind:propsdata="num"></app-header>
        <app-content v-on:pass="deliverNum"></app-content>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>     
        var appHeader = {
            template: '<div>header</div>',
            props: ['propsdata']
        }
        var appContent = {
            template: '<div>content<button v-on:click="passNum">pass</button></div>',
            methods: {
                passNum: function() {
                    this.$emit('pass', 10);
                }
            }
        }
    
        new Vue({            
            el: '#app',
            components: {
                'app-header': appHeader,
                'app-content': appContent
            },
            data: {
                num: 0
            },
            methods: {
                deliverNum: function(value) {
                    this.num = value;
                }
            }
        })
    </script>
</body>
</html>
~~~