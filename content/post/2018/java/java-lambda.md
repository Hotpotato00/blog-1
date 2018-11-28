---
draft: false
date: 2018-11-29T00:10:00+09:00
title: "자바의 정석 - 람다식(Lambda Expression)"
slug: "java-lambda"
categories: ["2018", "java"]
tags: ["java"]
---

>자바의 정석(남궁성 저) 2권 학습내용 정리  


## 1. 람다식
>메서드를 하나의 식(expression)으로 표현한 것.  
>메서드를 람다식으로 표현하면 메서드의 이름과 반환값이 없어지므로 람다식을 익명함수(anonymous function)라고도 한다.  

람다식은 메서드의 매개변수로 전달될 수 있고,  
메서드의 결과로 반환될 수 있다.  
즉, 메서드를 변수처럼 다루는 것이 가능하다.

## 2. 람다식 작성하기
- 메서드에서 이름과 반환타입 제거
- 매개변수 선언부와 몸통{} 사이에 -> 추가

~~~java
//기존
반환타입 메서드이름 (매개변수 선언)  {
  ...
}

//람다식
(매개변수 선언) ->  {
  ...
}
~~~

- 반환값이 있는 메서드는 return 대신 식(expression)으로 대신할 수 있다.(연산 결과가 자동으로 반환값이 되고 ; 생략)
- 매개변수의 타입은 추론가능하면 생략 가능 (대부분 생략 가능)
- 두 매개변수 중 하나의 타입만 생략하는 것은 불가능
- 매개변수가 하나뿐이면 괄호() 생략 가능
- 중괄호{} 안의 문장이 하나일 때는 중괄호{} 생략 가능 (문장 끝에 ; 생략)
- 중괄호{} 안의 문장이 return문일경우 중괄호{} 생략 불가능

~~~java
//기존
int max(int a, int b) {
  return a > b ? a : b;
}

//람다식
(int a, int b) -> {
  return a > b ? a : b;
}

//return문 대신 expression 사용
(int a, int b) -> a > b ? a: b

//매개변수 타입 생략
(a, b) -> a > b ? a : b

//매개변수 1개일 경우 괄호 생략
a -> a*a     //OK
int a -> a*a //에러

//본문 문장 1개일 경우 중괄호 생략
(String name, int i) -> System.out.println(name+"="+i)
~~~


## 3. 함수형 인터페이스(Functional Interface)
>람다식을 다루기 위한 인터페이스

람다식은 메서드와 동등한 것이 아니라 익명클래스의 객체와 동등하다.

~~~java
// 람다식
  (int a, int b) -> a > b ? a : b

// 익명클래스의 객체
  new Object()  {
    int max(int a, int b) {
      return a > b ? a : b ;
    }
  }
~~~

람다식으로 정의된 익명 객체의 메서드를 호출하려면 참조변수가 필요하다.  
이 때, 참조변수의 타입은 클래스 또는 인터페이스가 가능한데,  
람다식과 동등한 메서드가 정의되어 있는 것이어야 한다.

~~~java
// 예를 들어 max() 메서드가 정의된 Myfunction 인터페이스 정의
  interface MyFunction  {
    public abstract int max(int a, int b);

// MyFunction 인터페이스를 구현한 익명클래스 객체 생성
  MyFunction f = new MyFunction() {
    public int max (int a, int b);
      return a > b ? a : b;
    }
  }
  int big = f.max(5, 3);  //익명 객체의 메서드 호출

// 위의 익명 객체를 람다식으로 대체
  MyFunction f = (int a, int b) -> a > b ? a : b;
  int big = f.max(5, 3);
  ~~~

위 처럼 MyFunction 인터페이스를 구현한 익명 객체를 람다식으로 대체 가능한 이유는 람다식도 실제로는 익명 객체이고, 
MyFunction 인터페이스를 구현한 익명 객체의 메서드 max()와 람다식의 매개변수의 타입과 개수, 반환값이 일치하기 때문이다.  

단, 함수형 인터페이스에는 오직 하나의 추상 메서드만 정의되어 있어야 한다. 그래야 람다식과 인터페이스가 1:1로 연결되기 때문이다. 
반면 static 메서드와 default 메서드의 개수에는 제약이 없다.

@FunctionalInterface를 붙이면 컴파일러가 함수형 인터페이스를 올바르게 정의하였는지 확인해준다.

~~~java
// 기존 인터페이스의 메서드 구현
  List<String> list = Arrays.asList("abc", "aaa", "bbb", "ccc");
  Collections.sort(list, new Comparator<String>() {
    public int compare(String s1, String s2)  {
      return s2.compareTo(s1);
    }
  });

// 람다식으로 구현
  List<String> list = Arrays.asList("abc", "aaa", "bbb", "ccc");
  Collections.sort(list, (s1, s2) -> s2.compareTo(s1));
~~~

함수형 인터페이스로 람다식을 참조할 수 있지만, 람다식의 타입이 함수형 인터페이스의 타입과 일치하는 것은 아니다. 
람다식은 익명 객체이고 익명 객체는 타입이 없다. (정확히는 타입이 있지만 컴파일러가 임의로 이름을 정하기 때문에 알 수 없다.) 
그러므로 아래와 같이 형변환이 필요하다.

~~~java
MyFunction f = (MyFunction) (()->{}); //
~~~

람다식은 MyFunction인터페이스를 직접 구현하지 않았지만, 이 인터페이스를 구현한 클래스의 객체와 완전히 동일하기 때문에 위와 같은 형변환을 허용한다. 
그리고 이 형변환은 생략 가능하다.

람다식은 이름이 없을 뿐 분명히 객체인데도, Object 타입으로 형변환 할 수 없다. 람다식은 오직 함수형 인터페이스로만 형변환이 가능하다. 
굳이 Object 타입으로 형변환하려면 아래와 같이 먼저 함수형 인터페이스로 변환해야 한다.

~~~java
Object obj = (Object)(MyFunction)(()->{});
String str = (Object)(MyFunction)(()->{})).toString();
~~~


## 4. java.util.function 패키지
>이 패키지에 자주 쓰이는 형식의 메서드를 함수형 인터페이스로 정의해놓았다.

|함수형 인터페이스|메서드|설명|
|---|---|---|
|java.lang.Runnable|void run()|매개변수도 없고, 반환값도 없음|
|Supplier<T>|T get()|매개변수는 없고, 반환값만 있음|
|Consumer<T>|void accept(T t)|Supplier와 반대로 매개변수만 있고, 반환값이 없음|
|Function<T,R>|R apply(T t)|일반적인 함수. 하나의 매개변수를 받아서 결과를 반환|
|Predicate<T>|boolean test(T t)|조건식을 표현하는데 사용. 매개변수는 하나, 반환 타입은 boolean|
|BiConsumer<T,U>|void accept(T t, U u)|두개의 매개변수만 있고, 반환값이 없음|
|BiPredicate<T,U>|boolean test(T t, U u)|조건식을 표현하는데 사용됨. 매개변수는 둘, 반환값은 boolean|
|BiFunction<T,U,R>|R apply(T t, U u)|두개의 매개변수를 받아서 하나의 결과를 반환|


- 수학에서 결과로 true 또는 false를 반환하는 함수를 Predicate 라고 한다.  
- 매개변수가 2개인 함수형 인터페이스는 이름 앞에 'Bi'가 붙는다.  
- Supplier는 매개변수는 없고 반환값만 존재하는데 메서드는 두 개의 값을 반환할 수 없으므로 BiSupplier가 없다.  
- 매개변수의 타입과 반환타입이 일치할 때는 Function 대신 UnaryOperator를 사용한다. (매개 변수 2개면 BinaryOperator)

~~~java
// 조건식 표현에 사용되는 Predicate

Predicate<String> isEmptyStr = s -> s.length() == 0;
String s = "";

if(isEmptyStr.test(s))
  System.out.println("This is an empty String.");
~~~


## 5. Function의 합성과 Predicate의 결합

### 5.1. Function의 합성
>두 람다식을 합성해서 새로운 람다식을 만들 수 있다.  

함수 f, g가 있을 때  
f.andThen(g)는 함수 f를 먼저 적용하고 g 적용.  
f.compose(g)는 함수 g를 먼저 적용하고 f 적용.

### 5.2. Predicate의 결합
>여러 Predicate를 and(), or(), negate()로 연결해서 하나의 새로운 Predicate로 결합할 수 있다. 
>Predicate의 끝에 negate()를 붙이면 조건식 전체가 부정이 된다.

~~~java
Predicate<Integer> p = i -> i < 100;
Predicate<Integer> q = i -> i < 200;
Predicate<Integer> r = i -> i%2 == 0;
Predicate<Integer> notP = p.negate();

// 100 <= i && (i < 200 || i%2==0)
Predicate<Integer> all = notP.and(q.or(r));
System.out.println(all.test(150));  // true
~~~

static 메서드인 isEqual()은 두 대상을 비교하는 Predicate를 만들 때 사용한다.  
isEqual()의 매개변수로 비교대상을 하나 지정하고, 또 다른 비교대상은 test()의 매개변수로 지정한다.

~~~java
Predicate<String> p = Predicate.isEqual(str1);
boolean result = p.test(str2);  //str1과 str2가 같은지 비교하여 결과를 반환

// 위의 두 문장을 하나로 합치면
boolean result = Predicate.isEqual(str1).test(str2);
~~~


## 6. 메서드 참조(Method reference)
>람다식이 하나의 메서드만 호출하는 경우, 메서드 참조를 통해 람다식을 간략히 할 수 있다.  
>클래스명::메서드명 또는 참조변수::메서드명

~~~java
// 기존
Function<String, Integer> f = (String s) -> Integer.parseInt(s);

// 메서드 참조
Funcation<String, Integer> f = Integer::parseInt;
~~~

생성자를 호출하는 람다식도 메서드 참조로 변환 가능

~~~java
Supplier<MyClass> s = () -> new MyClass();  // 람다식
Supplier<MyClass> s = MyClass::new; // 메서드 참조
~~~

배열 생성할 경우

~~~java
Function<Integer, int[]> f = x -> new int[x]; // 람다식
Function<Integer, int[]> f2 = int[]::new; // 메서드 참조
~~~