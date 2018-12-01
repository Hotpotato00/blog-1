---
draft: false
date: 2018-12-02T00:37:49+09:00
title: "자바의 정석 - 직렬화(Serialization)"
slug: "java-serialization"
categories: ["2018", "java"]
tags: ["java"]
---

>자바의 정석(남궁성 저) 2권 학습내용 정리  

## 1. 직렬화란?
>객체를 데이터 스트림으로 만드는 것  
>객체에 저장된 데이터를 스트림에 쓰기 위해 연속적인 데이터로 변환하는 것  
>스트림으로부터 데이터를 읽어서 객체를 만드는 것은 역직렬화

### * 객체 정의
객체는 클래스에 정의된 인스턴스변수의 집합.  
객체에는 클래스변수나 메서드가 포함되지 않는다.  
객체는 오직 인스턴스변수들로만 구성.

`객체를 저장한다는 것`은 객체의 모든 인스턴스변수의 값을 저장하는 것.  
`두 객체가 동일한지 판단하는 기준`은 두 객체의 인스턴스변수의 값들이 같은지에 따른다.

## 2. ObjectInputStream, ObjectOutputStream
>직렬화 = 스트림에 객체를 출력 = ObjectOutputStream 사용  
>역직렬화 = 스트림으로부터 객체를 입력 = ObjectInputStream 사용

### 2.1. ObjectInputStream과 ObjectOutputStream은 보조스트림이라 기반스트림이 필요하다.

~~~java
ObjectInputStream(InputStream in)
ObjectOutputStream(OutputStream out)
~~~

### 2.2. 파일에 객체를 저장(직렬화) 방법

~~~java
FileOutputStream fos = new FileOutputStream("objectfile.ser");
ObjectOutputStream out = new ObjectOutputStream(fos);

out.writeObject(new Member());  //objectfile.ser 파일에 Member 객체를 직렬화하여 저장
~~~

### 2.3. 역직렬화 방법
~~~java
FileInputStream fis = new FileInputStream("objectfile.ser");
ObjectInputStream in = new ObjectInputStream(fis);

Member member = (Member)in.readObject();  //readObject()의 반환타입이 Object라서 형변환 필요
~~~

## 3. 직렬화 가능한 클래스 만들기
>직렬화하고자 하는 클래스가 java.io.Serializable 인터페이스를 구현하면 된다.

### 3.1. Serializable을 구현한 클래스를 상속받아도 직렬화가 가능하다.
~~~java
public class SuperMember implements Serializable  {
  String name;
  String password;
}
public class Member extends SuperMember {
  int age;
}
~~~

### 3.2. Object객체는 직렬화할 수 없지만, 실제 저장된 객체의 종류에 따른다.
~~~java
public class Member implements Serializable {
  String name;
  String password;
  int age;
  
  Object obj = new Object();  // Object객체는 직렬화 불가능
  Object obj2 = new String("abc");  // 실제 저장된 객체의 종류가 String 이므로 직렬화 가능.
}
~~~

### 3.3. 제어자 transient로 직렬화 대상에서 제외시키기
~~~java
public class Memebr implements Serializable {
  String name;
  transient String password;  // 직렬화 대상에서 제외
  int age;
  transient Object obj = new Object();  // 직렬화 대상에서 제외
~~~


## 4. 직렬화/역직렬화 예제

### 4.1. 생성한 객체를 직렬화하여 파일에 저장

~~~java
public static void main(String[] args)  {
  
  try {
      String fileName = "Member.ser";
      
      FileOutputStream fos = new FileOutputStream(fileName);
      BufferedOutputStream bos = new BufferedOutputStream(fos);
      ObjectOutputStream out = new ObjectOutputStream(bos);
      
      Member m1 = new Member("JavaMan", "1234", 29);
      Member m2 = new Member("JavaWoman", "0000", 29);
      
      ArrayList<Member> list = new ArrayList<>();
        list.add(m1);
        list.add(m2);
      
      // 객체 직렬화
      out.writeObject(m1);
      out.writeObject(m2);
      out.writeObject(list);
      
      out.close();
      
  } catch(IOException e)  {
      e.printStackTrace();
  }
}
~~~

### 4.2. 위의 직렬화한 객체를 역직렬화

~~~java
public static void main(String[] args)  {
  
  try {
      String fileName = "Member.ser";
      FileInputStream fis = new FileInputStream(fileName);
      BufferedInputStream bis = new BufferedInputStream(fis);
      ObjectInputStream in = new ObjectInputStream(bis);
      
      // 객체를 읽을 때는 출력한 순서와 일치해야함.
      // 그러므로 객체가 많을 때는 ArrayList 같은 컬렉션에 저자해서 직렬화하는 것이 편함.
      Memebr m1 = (Member)in.readObject();
      Member m2 = (Member)in.readObject();
      ArrayList list = (ArrayList)in.readObject();
      
      in.close();
  } catch (Exception e) {
    e.printStackTrace();
  }
}
~~~
