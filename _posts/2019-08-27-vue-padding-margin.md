---
layout: post
title: 'Vue : Padding, Margin'
author: kjham.ham
date: 2019-08-27 11:00
tags: [swtech,java,vuejs,frontend]
image: /files/covers/VUE_JS.jpg
---

Vuetify를 이용해 Tag를 작성하여 Component를 배치하다 보면,  
Padding, Margin을 이용하여 Styling해야 할 일이 많습니다.  

태그의 style 속성 값에 줄 수도 있지만,  
보다 쉽게 적용할 수 있도록 각 태그 안에 class를 이용하여 적용할 수 있습니다.  


~~~html
<v-text-field
class="mt-0 mb-2 pt-1"
> </v-text-field>
~~~
위 예를 해석하면,  
Vuetify의 Text Field 컴포넌트를 배치할 때  
Margin Top은 0px, Margin Bottom은 2px, Padding Top은 1px를 주겠다는 것입니다.  

즉, p-2, pt, pb, mt 등을 이용하여 보다 쉽게 padding, margin을 정의할 수 있습니다.  

> 각 키워드의 의미  
p: padding  
m: margin  
t: top  
b: bottom  
r: right  
l: left  

이를 응용하면 다음과 같은 의미를 갖습니다.  
p-2 : 전체 Padding 2px  
pb-2 : 아래 Padding 2px  
m-1 : 전체 margin 1px  
mr-3 : 오른쪽 margin 3px  