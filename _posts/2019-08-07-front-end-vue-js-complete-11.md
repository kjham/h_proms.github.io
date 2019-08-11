---
layout: post
title: 'Vue.js 완벽가이드 - 11'
author: kjham.ham
date: 2019-08-07 16:00
tags: [swtech,java,vue-js,front-end]
image: assets/images/VUE_JS.jpg
---

# 11. 외부 라이브러리 모듈화 방법  

vus.js에서 외부 라이브러리를 사용하는 방법을 알아봅니다.  

Vue.js 관련 라이브러리가 없을 때 일반 라이브러리를 결합할 수 있어야 하기 때문에..  
외부 라이브러리 모듈 방법을 알고 있어야 합니다.  

다음 사이트에서 개발자가 사용하는 framework 통계를 확인할 수 있습니다.  
https://2018.stateofjs.com/front-end-frameworks/overview/  

## 11.1 chart.js 적용  
`npm create chart-lib` 로 새로운 프로젝트를 생성합니다.  
`npm install chart.js --save`를 실행하여 chart.js를 설치합니다.  
`package.json`내에 chart.js가 설치된 것을 확인할 수 있습니다.

`App.vue`의 내용을 모두 제거하고, 다음과 같이 입력합니다.  
`export default` 하위에 컴포넌트 속성이나 인스턴스 옵션을 정의해야 합니다..  
`mounted` 속성 내 chart.js의 사용방법을 참고합니다.  
~~~vue
<template>
  <div>
    <h1>Chart.js</h1>
    <canvas id="myChart" width="400" height="400"></canvas>
  </div>
</template>

<script>
import Chart from 'chart.js';

export default {
  // 컴포넌트 속성 && 인스턴스 옵션
  mounted() {
    var ctx = document.getElementById('myChart');
    var myChart = new Chart(ctx, {
        type: 'bar',
        data: {
            labels: ['Red', 'Blue', 'Yellow', 'Green', 'Purple', 'Orange'],
            datasets: [{
                label: '# of Votes',
                data: [12, 19, 3, 5, 2, 3],
                backgroundColor: [
                    'rgba(255, 99, 132, 0.2)',
                    'rgba(54, 162, 235, 0.2)',
                    'rgba(255, 206, 86, 0.2)',
                    'rgba(75, 192, 192, 0.2)',
                    'rgba(153, 102, 255, 0.2)',
                    'rgba(255, 159, 64, 0.2)'
                ],
                borderColor: [
                    'rgba(255, 99, 132, 1)',
                    'rgba(54, 162, 235, 1)',
                    'rgba(255, 206, 86, 1)',
                    'rgba(75, 192, 192, 1)',
                    'rgba(153, 102, 255, 1)',
                    'rgba(255, 159, 64, 1)'
                ],
                borderWidth: 1
            }]
        },
        options: {
            scales: {
                yAxes: [{
                    ticks: {
                        beginAtZero: true
                    }
                }]
            }
        }
    });
  }
}
</script>

<style>
</style>
~~~

그리고 `npm run serve`로 서버를 기동하면, 차트가 표현된 것을 볼 수 있습니다.  

## 11.2 Bar Chart 컴포넌트 생성  
`BarChart.vue`를 생성하여 컴포넌트화를 진행합니다.  
~~~vue
<template>
    <div>
        <canvas id="myChart" width="400" height="400"></canvas>
    </div>
</template>

<script>
import Chart from 'chart.js';

export default {
    mounted() {
        var ctx = document.getElementById('myChart');
        var myChart = new Chart(ctx, {
            type: 'bar',
            data: {
                labels: ['Red', 'Blue', 'Yellow', 'Green', 'Purple', 'Orange'],
                datasets: [{
                    label: '# of Votes',
                    data: [12, 19, 3, 5, 2, 3],
                    backgroundColor: [
                        'rgba(255, 99, 132, 0.2)',
                        'rgba(54, 162, 235, 0.2)',
                        'rgba(255, 206, 86, 0.2)',
                        'rgba(75, 192, 192, 0.2)',
                        'rgba(153, 102, 255, 0.2)',
                        'rgba(255, 159, 64, 0.2)'
                    ],
                    borderColor: [
                        'rgba(255, 99, 132, 1)',
                        'rgba(54, 162, 235, 1)',
                        'rgba(255, 206, 86, 1)',
                        'rgba(75, 192, 192, 1)',
                        'rgba(153, 102, 255, 1)',
                        'rgba(255, 159, 64, 1)'
                    ],
                    borderWidth: 1
                }]
            },
            options: {
                scales: {
                    yAxes: [{
                        ticks: {
                            beginAtZero: true
                        }
                    }]
                }
            }
        });
    }
}
</script>

<style>

</style>
~~~

그리고 `App.vue`에서 `BarChart.vue`를 컴포넌트로 추가하여 표현합니다.  
~~~vue
<template>
  <div>
    <h1>Chart.js</h1>
    <bar-chart></bar-chart>
  </div>
</template>

<script>
import BarChart from './components/BarChart.vue';
export default {
  components: {
    BarChart,
  }
}
</script>

<style>
</style>
~~~

## 11.3 플러그인 제작  
Chart를 여러 종류를 사용할 경우,  차트를 사용하는 모든 페이지에 공통된 import를 입력해야 합니다.  
Chart를 Vuex, VurRouter 처럼 플러그인으로 추가하여 import 없이 사용할 수 있도록 합니다.  
`/plugins/ChartPlugin.js` 를 생성합니다.  
~~~js
import Chart from 'chart.js';

export default {
    install(Vue) {
        // 객체의 유형을 확장하는 것을 prototype이라고 합니다.
        // $_는 충돌을 회피하기 위해 권고되는 표현방식 입니다.
        Vue.prototype.$_Chart = Chart;
        
        // 다음과 같이 공용으로 사용할 수 있습니다.
        // // App.vue
        // this.$_Chart

        // // LineChar.vue
        // this.$_Chart
    }
}
~~~

`main.js`에 플러그인을 사용하도록 코드를 작성합니다.  
~~~js
import Vue from 'vue'
import App from './App.vue'
import ChartPlugin from './plugins/ChartPlugin.js';

Vue.config.productionTip = false

Vue.use(ChartPlugin);

new Vue({
  render: h => h(App),
}).$mount('#app')
~~~

이제 모든 컴포넌트에서 Chart를 바로 사용할 수 있습니다.  
라인 차트를 추가해봅시다. `/components/LineChart.vue` 입니다.  
`canvas` 태그 안에 ref가 추가하면 `this.$refs.lineChart` 처럼 쉽게 부를 수 있습니다.  
그리고 `this.$_Chart` 로 쉽게 차트를 불러오는 것을 볼 수 있습니다.  
~~~vue
<template>
    <div>
        <canvas ref="lineChart" id="lineChart" width="400" height="400"></canvas>
    </div>
</template>

<script>
export default {
    mounted() {
        // var ctx = document.getElementById('lineChart').getContext('2d');
        // var ctx = this.$refs.lineChart.getContext('2d');
        var chart = new this.$_Chart(this.$refs.lineChart.getContext('2d'), {
            // The type of chart we want to create
            type: 'line',

            // The data for our dataset
            data: {
                labels: ['January', 'February', 'March', 'April', 'May', 'June', 'July'],
                datasets: [{
                    label: 'My First dataset',
                    backgroundColor: 'rgb(255, 99, 132)',
                    borderColor: 'rgb(255, 99, 132)',
                    data: [0, 10, 5, 2, 20, 30, 45]
                }]
            },

            // Configuration options go here
            options: {}
        });
    }
}
</script>

<style>
</style>
~~~
