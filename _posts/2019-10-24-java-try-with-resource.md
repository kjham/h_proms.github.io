---
layout: post
title: 'try-with-resource 사용'
author: kjham.ham
date: 2019-10-24 09:30
tags: [swtech,java,effectivejava]
image: /files/covers/effectivejava.jpg
---

# try-finally 보다는 try-with-resource를 사용하라

자바 라이브러리는 close 메서드를 호출해 직접 닫아줘야 하는 자원이 많습니다.  
안전망으로 finalize가 있지만 많은 문제가 있기 때문에 썩 좋지 않습니다.  
전통적으로는 `try-finally`를 많이 사용하였습니다.  
~~~java
static String readLine(String path) throws IOException {
    BufferedReader br = new BufferedReader(new FileReader(path));
    try {
        return br.readLine();        
    } finally {
        br.close();
    }
}
~~~

하지만 자원을 하나 더 사용하게 되면 코드가 지저분하게 되고, close를 놓치기도 합니다.  
~~~java
static void copy(String src, String dst) throws IOException {
    InputStream in = new FileInputStream(src);
    try {
        OutputStream out = new FileOutputStream(dst);
        try {
            byte[] buf = new Byte[BUFFER_SIZE];
            int n;
            while((n = in.read(buf)) >= 0)
                out.write(buf, 0, n);
        } finally {
            out.close();
        }
    } finally {
        in.close();
    }
}
~~~
또한 물리적으로 파일에 문제가 생겼을 때, `out, in` 객체 모두 Exception을 던졌을 때
스택 추적 내용에 `out`에 대한 정보가 누락되기 때문에 디보깅을 몹시 어렵게 합니다.  

자바 7부터 `try-with-resource`를 지원하면서 이 모든 문제가 해결되었습니다.  
`AutoCloseable` 인터페이스를 구현하기만 하면 모든 객체에 적용할 수 있습니다.  
~~~java
static void copy(String src, String dst) throws IOException {
    try (InputStream in = new FileInputStream(src);
            OutputStream out = new FileOutputStream(dst)) {
        byte[] buf = new byte[BUFFER_SIEZ];
        int n;
        while((n = in.read(buf)) >= 0)
            out.write(buf, 0, n);
    }
~~~

코드도 매우 간결해졌고, 예외가 양쪽 모두에서 발생하면 close에서 발생한 예외는 숨겨지고
readLine에서 발생한 예외가 기록됩니다.  
또한 숨겨진 에러는 버려지지않고 `숨겨졌다(suppressed)`는 꼬리표를 달고 출력됩니다.  
자바 7의 `Throwable`에 추가된 `getSuppressed` 메서드를 이용하면 프로그램 코드도 가져올 수 있습니다.  

그리고 `try-finally` 처럼 `catch`도 사용할 수 있습니다.

> 핵심 정리  
꼭 회수해야 하는 자원을 다룰 때는 `try-finally` 말고, `try-with-resource`를 사용하자.  
예외는 없다. 코드는 더 짧고 분명해지고, 만들어지는 예외 정보도 훨씬 유용하다.  
`try-finally`로 작성하면 실용적이지 못할 만큼 코드가 지저분해지는 경우라도, `try-with-resource`로는 정확하고 쉽게 자원을 회수할 수 있다.