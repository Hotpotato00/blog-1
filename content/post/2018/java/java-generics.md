---
draft: false
date: 2018-10-25T00:28:04+09:00
title: "자바의 정석 - 지네릭스(Generics)"
slug: "java-generics"
categories: ["2018", "java"]
tags: ["java"]
---

>자바의 정석(남궁성 저) 학습내용 정리

## 1. 지네릭스(Generics)
>지네릭스는 다양한 타입의 객체들을 다루는 메서드나 컬렉션 클래스에 컴파일 시의 타입 체크를 해주는 기능  
>다시 말해, 다룰 객체의 타입을 미리 명시해줌으로써 번거로운 형변환을 줄여준다.

지네릭 타입은 클래스와 메서드에 선언할 수 있다.  

~~~java
class Box<T>  { // 지네릭 타입 T를 선언. T는 타입변수
  T item;
  
  void setItem(T item)  {
    this.item = item;
  }
  
  T getItem() {
    return item;
  }
}
~~~

지네릭 클래스가 된 Box 클래스의 객체를 생성할 때는  
다음과 같이 참조변수와 생성자에 타입 T대신 사용될 실제 타입을 지정해야 한다.  

~~~java
Box<String> b = new Box<String>();  // 타입 T대신 실제 타입 지정
b.setItem(new Object());  // 에러. String 외의 타입은 지정 불가
b.setItem("ABC"); // OK. String 타입이므로 가능
~~~

예를 들어, Box<String>과 Box<Integer>는  
지네릭 클래스 Box<T>에 서로 다른 타입을 대입하여 호출한 것일 뿐, 서로 다른 클래스를 의미하지 않는다.  
컴파일 후에 Box<String>과 Box<Integer>는 이들의 원시타입인 Box로 바뀐다. 즉, 지네릭타입이 제거된다.

## 2. 지네릭스 제한

- 모든 객체에 대해 동일하게 동작해야하는 static멤버에 타입변수 T를 사용할 수 없다.  
T는 인스턴스 변수로 간주되기 때문이다. (static 멤버는 인스턴스 변수를 참조할 수 없다.)  

- 지네릭 배열 타입의 참조변수를 선언하는 것은 가능하지만,  
`new T[10]` 과 같이 배열을 생성하는 것은 안된다.  
new 연산자 때문인데, 이 연산자는 컴파일 시점에 타입 T가 무엇인지 정확히 알아야 한다.  
instanceof 연산자도 같은 이유로 T를 피연산자로 사용할 수 없다.

## 3. 지네릭 클래스의 객체 생성과 사용

Apple이 Furuit의 자손이라고 가정해도  

~~~java
Box<Fruit> appleBox = new Box<Apple>(); // 에러. 대입된 타입이 다르다.
~~~

단, 두 지네릭 클래스의 타입이 상속관계에 있고, 대입된 타입이 같은 것은 괜찮다.

~~~java
Box<Apple> appleBox = new FruitBox<Apple>();  // OK. 다형성
~~~

생성된 Box<T>의 객체에 'void add(T item)'으로 객체를 추가할 때, 대입된 타입과 다른 타입의 객체는 추가할 수 없다.
~~~java
Box<Apple> appleBox = new Box<Apple>();
appleBox.add(new Apple());  // OK.
appleBox.add(new Grape());  // 에러. Box<Apple>에는 Apple 객체와 Apple의 자손만 추가 가능
~~~

## 4. 제한된 지네릭 클래스

지네릭 타입에 `extends`를 사용하면, 특정 타입의 자손들만 대입할 수 있게 제한할 수 있다.

~~~java
class FruitBox<T extends Fruit> { //Fruit의 자손만 타입으로 지정 가능
  ArrayList<T> list = new ArrayList<T>();
  ...
}
~~~

인터페이스를 구현해야 한다는 제약이 필요하다면, 이때도 implements 대신 `extends`를 사용한다.

~~~java
interface Eatable {}
class FruitBox<T extends Eatable> {
  ...
}
~~~

클래스 Fruit의 자손이면서 Eatable 인터페이스도 구현해야하면 `&` 기호로 연결한다.

~~~java
class FruitBox<T extends Fruit & Eatable> {
  ...
}
~~~

## 5. 와일드 카드

지네릭 타입은 컴파일러가 컴파일할 때만 사용하고 제거해버린다.  
때문에 지네릭 타입이 다른 것만으로는 Overloading이 성립하지 않고 '메서드 중복 정의'가 된다.  
이럴 때 사용하는 것이 와일드 카드이며, 와일드 카드는 어떤 타입도 될 수 있다. 기호 `?`로 표현한다.

`?`만으로는 Object타입과 다를 게 없으므로,  
`extends`와 `super`로 상한과 하한을 제한할 수 있다.

~~~java
<? extends T> T와 그 자손들만 가능
<? super T> T와 그 조상들만 가능
<?> 모든 타입 가능. <? extends Object>와 동일
~~~

와일드 카드에는 `&`을 사용할 수 없다.

## 6. 지네릭 메소드
>메소드의 선언부에 지네릭 타입이 선언된 메소드가 지네릭 메소드다.  
>지네릭 메소드는 지네릭 클래스가 아닌 클래스에도 정의할 수 있다.

~~~java
class FruitBox<T> {
    ...
  static <T> void sort(List<T> list, Comparator<? super T> c) {
    ...
  }
}
~~~

- 지네릭 클래스에 정의된 타입 매개변수와 지네릭 메소드에 정의된 타입 매개변수는 전혀 별개의 것이다.
- static 멤버에는 타입 매개변수를 사용할 수 없지만, 이처럼 메소드에 지네릭 타입을 선언하고 사용하는 것은 가능하다.
  - 메소드에 선언된 지네릭 타입은 지역 변수를 선언한 것과 같다고 생각하면 이해하기 쉽다.
  - 타입 매개변수는 메소드 내에서만 지역적으로 사용될 것이므로 메소드가 static이건 아니건 상관없다.
  - 같은 이유로, 내부 클래스에 선언된 타입 문자가 외부 클래스의 타입 문자와 같아도 구별될 수 있다.
  

