---
draft: false
date: 2018-10-03T13:20:33+09:00
title: "자바의 정석 - 예외 처리(Exception handling)"
slug: "java-exception"
categories: ["2018", "java"]
tags: ["java"]
---

>자바의 정석(남궁성 저) 학습내용 정리

## 1. 예외 처리 (Exception handling)
- 에러는 메모리 부족(OutOfMemoryError)이나 스택오버플로우(StackOverFlowError)처럼 일단 발생하면 복구할 수 없는 심각한 오류
- 예외는 발생하더라도 수습될 수 있는 오류

## 2. 예외 클래스 계층 구조
- Object
  - Throwable
    - ***Exception***
      - ***RuntimeException***
        - ArithmeticException
        - ClassCastException
        - NullPointerException
        - IndexOutOfBoundException
      - IOException
    - Error
- 예외 클래스는 ***Exception*** 클래스와 자식들과 ***RuntimeException*** 클래스와 자식들로 나뉜다.
- ***Exception*** 클래스들 : 사용자의 실수와 같은 외적인 요인에 의해 발생하는 예외
- ***RuntimeException*** 클래스들 : 개발자의 실수로 발생하는 예외
- 모든 예외 클래스는 Exception 클래스의 자손이므로, catch블럭의 괄호()에 Exception클래스 타입의 참조변수를 선언하면, 어떤 종류의 예외가 발생하더라도 이 catch블럭()에 의해서 처리된다.

## 3. printStackTrace()와 getMessage()
- 예외가 발생했을 때 생성되는 예외 클래스의 인스턴스에는 발생한 예외에 대한 정보가 담겨져 있으며, getMessage()와 printStackTrace()를 사용해서 이 정보를 얻을 수 있다.
- `printStackTrace()` : 예외발생 당시의 호출스택(Call Stack)에 있었던 메서드의 정보와 예외 메세지를 화면에 출력한다.
- `getMessage()` : 발생한 예외클래스의 인스턴스에 저장된 메세지를 얻을 수 있다.

## 4. 멀티 catch 블럭
- JDK1.7부터 여러 catch블럭을 `|`기호를 이용해서 하나의 catch 블럭으로 합칠 수 있다.
  {{< highlight java >}}
try {
...
} catch (ExceptionA e)  {
    e.printStackTrace();
} catch (ExceptionB e2) {
    e2.printStacktrace();
}
{{< /highlight >}}
- 이 try-catch 문을 아래와 같이 바꿀 수 있다.
  {{< highlight java >}}
try {
...
} catch (ExceptionA | ExceptionB e) {
    e.printStackTrace();
}
{{< /highlight >}}
- 단, 예외클래스들이 조상-자손 관계에 있으면 컴파일 에러가 발생한다.(불필요한 코드는 제거하라는 의미의 에러)

## 5. 메소드에 예외 선언
- 메소드에 예외를 선언하는 방법
  {{< highlight java >}}
void method() throws Exception1, Exception2, ... ExceptionN {
...
}
{{< /highlight >}}
- 메소드의 선언부에 예외를 선언함으로써 메소드를 사용하려는 사람이 메소드의 선언부를 보았을 때, 이 메소드를 사용하기 위해서는 어떠한 예외들이 처리되어져야 하는지 쉽게 알 수 있다.

## 6. finally 블럭
- finally 블럭은 try-catch문과 함께 예외의 발생여부에 상관없이 실행되어야할 코드를 포함시킬 목적으로 사용된다.
- finally 블럭은 try-catch문의 맨 마지막에 위치해야 한다.
- try 블럭에서 return문이 실행되는 경우에도 finally블럭의 문장들이 먼저 실행된 후에, 현재 실행 중인 메소드를 종료한다.
