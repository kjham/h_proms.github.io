
1. _config.yml  

이 곳에서 여러 세팅 정보를 관리합니다.  
다음과 같이 정의되어 있을 때, title 을 호출하고 싶을 때 {{ site.title }} 을 입력하면 됩니다.  
~~~js
title: SWDEVLAB 블로그
email: h_proms@naver.com
description: |
  Software Developement Laboratory
  & Life Style Blog
baseurl: ''
url: 'http://kjham.github.io'
timezone: Asia/Seoul 
~~~

2. _layouts  
블로그의 디자인과 직접적으로 연결된 레이아웃을 지정하는 파일이 저장된 폴더입니다.  
커스터마이징 할 때 가장 손이 많이 가는 곳이라고 할 수 있습니다.  
`post.html` 을 예로 봅시다.  
~~~html
---
layout: default
---
<div id="navbar" class="container">
    <h5><a id="link-back" href="/">Back to Posts</a></h5>
    {% include share.html page=page %}
</div>

<div id="cover" class="container"
     {% if page.image %}style="background-image: url({{site.url}}/{{ page.image }});"{% endif %}>
    <div>
        <h1>{{ page.title }}</h1>
        <p>
            {% for tag in page.tags %}
                <a href="{{site.url}}/tags/{{ tag }}">{{ tag }}</a>
                {% unless forloop.last %},{% endunless %}
            {% endfor %}
        </p>
        {% include cover_link.html cover=page.cover %}
    </div>
</div>
~~~

2-1. html, head, body TAG가 없습니다. 그 이유는 이 html 파일이 다른 파일에 삽입되는 코드이기 때문입니다.  
제일 위에 `layout: default` 은 `default.html`을 사용하고 거기에 이 파일을 `content`로 삽입하는 것을 의미합니다.  
1~3 라인은 `YAML 헤더` 라고 하는데 `Jekyll`은 이 헤더가 있는 파일을 특별하게 인식합니다.  
`default.html`을 보면 {{ content }} 가 있는 것을 볼 수 있습니다.  
~~~html
<!DOCTYPE html>
<html>
<head>
    {% include head.html %}
</head>
<body class="{{ page.layout | append: '-template' }}">

{% include header.html %}

<div id="wrapper">
    {{ content }}

    <div class="clearfix"></div>

    <a href="#" id="back-to-top"></a>
</div>
</body>
</html>
~~~

2-2. {{ }} 안에 page.xxx 라는 것들이 보입니다.  
이 두개의 중괄호로 감싸져있는 page.xxx는 이 파일을 레이아웃으로 사용할 파일에서 정의한 변수입니다.  
이 부분은 뒤의 _posts 부분에서 다시 상세하게 언급하겠습니다.

3. _includes  
위의 default.html 파일의 소스코드에서 설명하지 않았던 것이 하나 있습니다. 바로 { { include xxx } } 입니다.  
이 { { include xxx } }를 통해 _includes 폴더에 있는 파일을 추출하여 사용할 수 있습니다.  

4. _posts  
포스팅하기 위해 작성한 파일들이 이 폴더에 있습니다.  
특이한 점은 `YAML 헤더`가 위 부분에 있다는 것입니다.  
~~~html
---
layout: post
title: 'JBOSS'
author: kjham.ham
date: 2018-02-19 00:00
tags: [swtech,was,jboss]
image: /files/covers/blog.jpg
comments: true
---
~~~
`layout: post`는 `_layout` 폴더의 `post.html` 을 레이아웃으로 사용하겠다는 의미입니다.  
그리고 나머지 값은 `post.html` 에서 사용하게 될 변수들입니다.  
`{{page.title}}` 과 같이 사용하는 것이지요.

5. index.html  
Jekyll은 project 폴더에서 자동으로 index.html이라는 파일을 찾아 블로그의 첫 화면으로 렌더링합니다.  
즉, index.html 역시 기본적으로는 _posts의 포스트와 다르지 않다는 것이죠.  
단지 여러분들의 블로그에 들어오는 방문자들이 볼 가장 첫 화면이라는 점을 제외하고 말입니다.

