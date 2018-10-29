---
draft: false
date: 2018-10-30T01:39:34+09:00
title: "자바의 정석 - 쓰레드(Thread)"
slug: "java-thread"
categories: ["2018", "java"]
tags: ["java"]
---

>자바의 정석(남궁성 저) 2권 학습  

## 1. 프로세스와 쓰레드
- 프로세스란 실행중인 프로그램이다.  
- 프로그램을 실행하면 OS로부터 실행에 필요한 자원(메모리)를 할당받아 프로세스가 된다.
- 프로세스의 자원을 이용해서 실제로 작업을 수행하는 것이 쓰레드다.
- 모든 프로세스에는 최소한 하나 이상의 쓰레드가 존재한다.
- 쓰레드가 작업을 수행하려면 개별적인 메모리 공간(호출스택)이 필요하다.
- CPU의 코어(core)가 한 번에 단 하나의 작업만 수행할 수 있으므로, 동시에 처리되는 작업의 수는 코어의 개수와 일치한다.

## 2. 쓰레드의 구현과 실행
- 쓰레드를 구현하는 방법은 Thread 클래스를 상속받는 방법과 Runnable 인터페이스를 구현하는 방법이 있다.
- Thread 클래스를 상속받으면 다른 클래스를 상속받을 수 없기 때문에, Runnable 인터페이스를 구현하는 방법이 일반적이다.

### 2.1. Thread 클래스 상속

~~~java
class MyThread extends Thread {
  public void run() { // Thread 클래스의 run()을 오버라이딩
    작업 내용
  }
}
~~~

### 2.2. Runnable 인터페이스 구현
>Runnable 인터페이스는 오로지 run()만 정의되어 있는 간단한 인터페이스이다.  
>Runnable 인터페이스를 구현하기 위해서 해야 할 일은 추상메서드인 run()의 몸통 {}을 만들어 주는 것 뿐이다.

~~~java
public interface Runnable {
  public abstract void run();
}
~~~

>쓰레드를 구현한다는 것은 두 가지 방법 모두, 그저 쓰레드를 통해 작업하고자 하는 내용으로 run()의 몸통{}을 채우는 것 뿐이다.


### 2.3. 인스턴스 생성 방법
- Thread 클래스 상속

  ~~~java
  ThreadA t1 = new ThreadA();
  ~~~
  
- Runnable 인터페이스 구현

  ~~~java
  Runnable r = new ThreadB();
  Thread t2 = new Thread(r);
  ~~~

>Thread클래스를 상속받으면, 자손 클래스에서 조상인 Thread클래스의 메서드를 직접 호출할 수 있지만,  
Runnable을 구현하면 Thread클래스의 static메서드인 currentThread()를 호출하여 쓰레드에 대한 참조를 얻어 와야만 호출이 가능하다.

~~~java
class ThreadA extends Thread  {
  public void run() {
    System.out.println(getName());  // 조상인 Thread의 getName() 호출
  }
}
~~~

~~~java
class ThreadB implements Runnable {
  public void run() {
    System.out.println(Thread.currentThread().getName());
    // Thread.currentThread() - 현재 실행중인 Thread 반환
  }
}
~~~

### 2.4. 쓰레드의 실행
- 쓰레드를 생성했다고 자동을 실행되지 않는다. start()를 호출해야만 쓰레드가 실행된다.  
- start()가 호출되어도 바로 실행되는 것이 아니라, 실행대기 상태에 있다가 자신의 차례가 되어야 실행된다.
- 쓰레드의 실행 순서는 OS의 스케쥴러가 작성한 스케쥴에 의해 결정된다.
- 한 번 실행이 종료된 쓰레드는 다시 실행할 수 없다. (하나의 쓰레드에 start()가 한 번만 호출될 수 있다.)

## 3. Start() 와 run()
- main메서드에서 run()을 호출하는 것은 생성된 쓰레드를 실행시키는 것이 아니라, 단순히 클래스에 선언된 메서드를 호출하는 것일 뿐이다.
- start()는 새로운 쓰레드를 위해 호출스택(call stack)을 생성한 후에 run()을 호출해서, 생성된 호출스택에 run()이 첫 번째로 올라가게 한다.
  1. main메서드에서 쓰레드의 start()를 호출한다.
  2. start()는 새로운 쓰레드를 생성하고, 쓰레드가 작업하는데 사용될 호출스택을 생성한다.
  3. 새로 생성된 호출스택에 run()이 호출되어, 쓰레드가 독립된 공간에서 작업을 수행한다.
  4. 호출스택이 2개가 되었으므로 스케쥴러가 정한 순서에 의해 번갈아 가면서 실행된다.
- 주어진 시간동안 작업을 마치지 못한 쓰레드는 다시 자신의 차례가 돌아올 때까지 대기 상태로 있는다.
- 작업을 마친 쓰레드, 즉 run()의 수행이 종료된 쓰레드는 사용하던 호출스택이 모두 비워지고 사라진다.
  - 이는 자바프로그램을 실행하면 호출스택이 생성되고 main메서드가 처음으로 호출되고, 
  main메서드가 종료되면 호출스택이 비워지면서 프로그램도 종료되는 것과 같다.
- 한 쓰레드가 예외가 발생해서 종료되어도 다른 쓰레드의 실행에는 영향을 미치지 않는다.
