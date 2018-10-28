---
draft: false
date: 2018-10-28T19:29:48+09:00
title: "자바의 정석 - 열거형(Enums)"
slug: "java-enums"
categories: ["2018", "java"]
tags: ["java"]
---

>자바의 정석(남궁성 저) 학습내용 정리

## 1. 열거형(Enums)
>Java의 열거형은 C언어의 열거형과 다르게 열거형이 갖는 값 뿐만 아니라 타입까지 관리한다.  
>기존의 상수의 값이 바뀌면, 해당 상수를 참조하는 모든 소스를 다시 컴파일 해야한다. 하지만 열거형 상수를 사용하면 기존의 소스를 다시 컴파일하지 않아도 된다.

### 1.1. 사용방법

~~~java
enum 열거형이름 { 상수명1, 상수명2, ...}
~~~

- 열거형 상수 간의 비교에는 `==`을 사용할 수 있다. `equals()`가 아닌 `==`으로 비교가능한 것은 그만큼 빠르다는 것이다.
- 하지만 비교연산자(<, >)는 사용할 수 없고 `compareTo()`는 사용 가능하다.
- switch문의 조건식에 열거형 사용하기

  ~~~java
  void move() {
    switch(dir) {
        case EAST: x++; //Direction.EAST라고 쓰면 안된다.(열거형 이름 없이 상수 이름만 적어야 한다.)
          break;
        case WEST: x--;
          break;
        case SOUTH: y++;
          break;
        case NORTH: y--;
          break;
    }
  }
  ~~~
  
### 1.2. 열거형의 이해

~~~java
enum Direction { EAST, WEST, SOUTH, NORTH }
~~~
- 열거형 상수 하나하나가 Direction 객체이다.

위의 문장을 클래스로 정의하면,  
~~~java
class Direction {
  static final Direction EAST = new Direction("EAST");
  static final Direction WEST = new Direction("WEST");
  static final Direction SOUTH = new Direction("SOUTH");
  static final Direction NORTH = new Direction("NORTH");
  
  private String name;
  
  private Direction(String name)  {
    this.name = name;
  }
}
~~~

- Direction클래스의 static상수 EAST, WEST, SOUTH, NORTH의 값은 객체의 주소이고, 이 값은 바뀌지 않는 값이므로 `==`로 비교가 가능한 것이다.

### *추가학습
- [Java Enum 활용기 - 우아한형제들 기술블로그](http://woowabros.github.io/tools/2017/07/10/java-enum-uses.html)