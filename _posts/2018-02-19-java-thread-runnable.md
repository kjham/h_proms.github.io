---
layout: post
title: 'Thread Tutorial (1)'
author: kjham.ham
date: 2018-02-19 00:00
tags: [swtech,java,thread]
image: /files/covers/blog.jpg
comments: true
---

Java - Thread Tutorial (1) : Thread 와 Runnable

**Java**에서 여러 작업을 동시에 처리하고 싶은 경우나 비동기 적으로 작업을 수행하고자 할 때, Thread 라는 것을 사용합니다.

일반적으로 가장 많이 알려진 것은, Thread / Runnable 방식이 있습니다.
Thread 는 Class로 정의되어 있고, 실제 기능 동작을 위한 추상 메소드를 포함하고 있습니다.
따라서, Thread 만 사용하게 되는 경우 사용자 정의 클래스에 상속 받아서 사용해야 하지요.

~~~javascript
/////* Thread 방식 예제 */////
class CustomThread extends Thread
{
	public void run(){
		// 기능 동작 정의
	}

	public static void main(String[] args){
		// CustomThread 선언
		CustomThread customThread = new CustomThread();
		// Thread 기능동작 시작
		customThread.start();
		// Thread 완료 대기
		customThread.join();
	}
}

class MainApp{
	// CustomThread 선언
	CustomThread customThread = new CustomThread();
	// Thread 기능동작 시작
	customThread.start();
}
~~~

그러나 불필요한 Thread 상속을 하고싶지 않을 수 있습니다.
이 때 사용되는 것이 Interface 로 구현된 Runnable 입니다.
Runnable 을 선언하고, 실제 기능 동작할 메소드를 추상메소드에 넣고 Thread에 전달하기만 하면 됩니다.

{% highlight java linenos %}
/////* Thread & Runnable 방식 예제 */////
class MainApp{
	public static void main(String args[]){
		// CustomThread 선언
		Runnable runnable = new Runnable{
			public void run(){
				// 기능 동작 정의
			}
		}
		Thread thread = new Thread(runnable);
		// Thread 기능동작 시작
		thread.start();
		// Thread 완료 대기
		thread.join();
	}
}
{% endhighlight %}

Thread 는 비동기적으로 데이터를 처리합니다.
비동기적이라는 것은 **thread.start()** 에서 결과를 반환해주기 전까지 기다리지 않고, 바로 다음 코드를 진행한다는 겁니다.
따라서, 작업이 완료되는 것을 기다리기 위해 **thread.join()** 을 위 코드에서 사용하였습니다.

지금까지 Single-Thread 를 다루었습니다.
Thread Class 를 이용하여 Multi-Thread 방식을 취하려면, 여러 개의 Thread를 생성해야 합니다.
즉, List, Vector 등 자료구조로 Thread 에 대한 배열을 생성해야 하는 것이지요.

Multi-Thread 는 Java 의 ExecutorService 를 통해 소개하도록 하겠습니다.

이상 포스팅을 마치겠습니다.
