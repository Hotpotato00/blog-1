---
draft: false
date: 2018-10-03T12:33:20+09:00
title: "자바의 정석 - 추상클래스, 인터페이스"
slug: "java-abstract-interface"
categories: ["2018", "java"]
tags: ["java"]
---

>자바의 정석(남궁성 저) 학습내용 정리

## 1. 추상 클래스 (Abstract class)
- 미완성 메소드(추상메소드)를 포함하고 있다는 것을 제외하고는 일반클래스와 같다.
- 추상메소드가 없어도 `abstract`를 붙여서 추상클래스로 지정할 수 있다.
- 추상클래스는 인스턴스를 생성할 수 없다.
- 추상클래스는 상속을 통해서 자손클래스에 의해서만 완성된다.  
{{< highlight java >}}
abstract class 클래스이름  {
  ...
}
{{< /highlight >}}

## 2. 추상 메소드 (Abstract method)
- 선언부만 작성하고 구현부는 작성하지 않은 것이 추상메소드.
{{< highlight java>}}
/* 주석을 통해 기능 설명 */
abstract 리턴타입 메소드이름();
{{< /highlight >}}
- 추상클래스로부터 상속받는 자손클래스는 오버라이딩을 통해 조상인 추상클래스의 추상메소드를 모두 구현해야 한다.
- 만약 부모로 부터 상속받은 추상메소드 중 하나라도 구현하지 않으면 자식클래스 역시 추상클래스로 지정해야 한다.

## 3. 인터페이스
- 인터페이스는 일종의 추상클래스다.
- 인터페이스는 추상클래스처럼 추상메소드를 갖지만, 추상클래스와 달리 몸통을 갖춘 일반 메소드 또는 멤버변수를 가질 수 없다.
- 인터페이스는 오직 추상메소드와 상수만을 멤버로 가질 수 있다.

## 4. 인터페이스의 작성
- {{< highlight java >}}
interface PlayingCard {
  public static final int SPADE = 4;
  final int DIAMOND = 3; //public static final int DIAMOND = 3;
  static int HEART = 2;  //public static final int HEART = 2;
  int CLOVER = 1;        //public static final int CLOVER = 1;
  
  public abstract String getCardNumber();
  String getCardKind();  //public abstract String getCardKind();
}
{{< /highlight >}}
- 모든 멤버변수는 public static final 이어야하며, 이를 생략할 수 있다.  
- 모든 메서드는 public abstract 이어야하며, 이를 생략할 수 있다.  
  - 단, jdk 1.8부터 static 메서드와 디폴트 메서트는 예외
  
## 5. 인터페이스의 상속과 구현
- 인터페이스는 인터페이스로부터만 상속받을 수 있다.
- 클래스와 달리 다중 상속이 가능하다.
- 인터페이스도 추상클래스처럼 그 자체로는 인스턴스를 생성할 수 없다.
- 클래스는 `extends`(확장하다) 사용, 인터페이스는 `implements`(구현하다) 사용.
- 상속과 구현을 동시에 할 수 있다.
  {{< highlight java >}}
class Fighter extends Unit implements Fightable {
  public void move(int x, int y)  { /* 내용 생략 */ }
  public void attack(Unit u)  { /* 내용 생략 */ }
}
{{< /highlight >}}

## 6. 인터페이스를 이용한 다형성
- 리턴 타입이 인터페이스라는 것은 메소드가 해당 인터페이스를 구현한 클래스의 인스턴스를 반환한다는 것을 의미한다.
{{< highlight java>}}
Fightable method()  {
    ...
    Fighter f = new Fighter();
    return f;
}
{{< /highlight >}}

- repair 메소드의 매개변수의 타입을 Repairable로 선언하면, 이 메소드의 매개변수로 Repairable 인터페이스를 구현한 클래스의 인스턴스만 받아들여진다.
{{< highlight java >}}
interface Repairable() {}
class SCV extends GroundUnit implements Repairable {
    ...
}
class Tank extends GroundUnit implements Repairable {
    ...
}
class Drop extends AirUnit implements Repairable  {
    ...
}
{{< /highlight >}}
{{< highlight java >}}
void repair(Repairable r) {
    // 매개변수로 넘겨받은 유닛을 수리한다.
}
{{< /highlight >}}
- 앞으로 새로운 클래스가 추가될 때, SCV의 repair 메소드에 의해 수리가 가능하도록 하려면 Repairable 인터페이스를 구현하면 된다.
- 인터페이스 타입의 매개변수가 갖는 의미는 메소드 호출 시 해당 인터페이스를 구현한 클래스의 인스턴스를 매개변수로 제공해야 한다는 것이다.

## 7. default 메소드와 static 메소드
- 인터페이스에 원래 추상메소드만 선언할 수 있었는데, JDK1.8부터 default 메소드와 static 메소드도 추가할 수 있게 되었다.
- 인터페이스의 default메소드와 static메소드는 접근 제어자가 항상 public이며, 생략할 수 있다.
- default 메소드는 추상메소드의 기본적인 구현을 제공하는 메소드이다.
- default 메소드는 추상메소드가 아니기 때문에 default 메소드가 새로 추가되어도 해당 인터페이스를 구현한 클래스를 변경하지 않아도 된다.
- default 메소드는 추상메소드와 달리 일반 메소드처럼 몸통{}이 있어야 한다.

## 8. 내부클래스
- 내부 클래스는 클래스 내에 선언된 클래스다.
- 내부 클래스는 주로 AWT나 Swing과 같은 GUI어플리케이션의 이벤트처리 외에는 잘 사용하지 않는다.
- 내부 클래스의 종류와 특징
  - instance class
     - 외부 클래스의 멤버변수 선언위치에 선언.
     - 외부 클래스의 인스턴스 멤버처럼 다루어진다.
     - 주로 외부 클래스의 인스턴스 멤버들과 관련된 작업에 사용될 목적으로 선언된다.
  - static class
     - 외부 클래스의 멤버변수 선언위치에 선언.
     - 외부 클래스의 static 멤버처럼 다루어진다.
     - 특히 static 메소드에서 사용될 목적으로 선언된다.
  - local class
     - 외부 클래스의 메소드나 초기화블럭 안에 선언.
     - 선언된 영역 내부에서만 사용될 수 있다.
  - anonymous class
     - 클래스의 선언과 객체의 생성을 동시에 하는 이름없는 클래스(일회용)