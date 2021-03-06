---
draft: false
date: 2018-10-03T14:01:53+09:00
title: "자바의 정석 - 날짜와 시간(Calendar와 Date, java.time패키지)"
slug: "java-calendar-date"
categories: ["2018", "java"]
tags: ["java"]
---

>자바의 정석(남궁성 저) 학습내용 정리

## 1. Calendar 와 Date
- `Date`는 JDK1.0, `Calendar`는 JDK1.1 부터 제공되었다.
- JDK1.8부터 `java.time 패키지`로 Calendar와 Date의 단점을 개선한 클래스들이 추가되었다.
- `Calendar`는 추상클래스라서 객체를 직접 생성할 수 없고, 메소드를 통해서 완전히 구현된 클래스의 인스턴스를 얻어야 한다.
{{< highlight java >}}
// 에러. 추상클래스는 인스턴스를 생성할 수 없다.
Calendar cal = new Calendar(); 

// 굿. getInstance()는 Calendar 클래스를 구현한 클래스의 인스턴스를 반환
Calendar cal = Calendar.getInstance();
{{< /highlight >}}
- `getInstance()`가 `static`인 이유
  - 메소드 내의 코드에서 인스턴스 변수를 사용하거나 인스턴스 메소드를 호출하지 않기 때문
  - `static`이 아니라면 객체를 생성한 다음에 호출해야 하는데 Calendar는 추상클래스라 객체를 생성할 수 없기 때문

### 1.1. Calendar 와 Date 간 변환
- Calendar가 추가되면서 Date는 대부분의 메소드가 `deprecated` 되었다.
- Java API 문서에서 더 이상 사용을 권장하지 않는 대상에 `deprecated`를 붙였다.
{{< highlight java >}}
//1. Calendar를 Date로 변환
    Calendar cal = Calendar.getInstance();
      ...
    Date d = new Date(cal.getTimeInMillis());

//2. Date를 Calendar로 변환
    Date d = new Date();
      ...
    Calendar cal = Calendar.getInstance();
    cal.setTime(d);
{{< /highlight >}}
- get(Calendar.MONTH)로 얻어오는 값은 1\~12가 아니라 0\~11이다. 그래서 0이 1월 11이 12월을 의미한다.


### 1.2. 두 날짜간의 차이 구하기
  - 두 날짜를 최소단위인 초단위로 변경한 다음 그 차이를 구한다.

### 1.3. 시간상의 전후 알기
  - 두 날짜간의 차이가 양수인지 음수인지 판단
  - `boolean after(Object when)` 또는 `boolean before(Object when)` 사용

### 1.4. 특정 날짜/시간 기준 일정 기간 전후의 날짜/시간 알기
  - `add(int field, int amount)`
  - `roll(int field, int amount)`
    - `add()`와의 차이점은 다른 필드에 영향 미치지 않음
    - 단, Calendar.DATE가 말일일 때, `roll`로 Calendar.MONTH를 변경하면 Calendar.DATE 필드에 영향을 미친다.

### 1.5. 해당 월의 마지막날 알기
  - 다음 달의 1일에서 하루 빼기
  - `getActualMaximum(Calendar.DATE)` 사용

### 1.6. 일 수 계산
  - Calendar는 1970년 1월 1일을 기준으로 계산
  - 1970년 1월 1일 이전에 날짜에 대해 `getTimeInMillis()`를 호출하면 음수 반환
  
## 2. 형식화 클래스

### 2.1. DecimalFormat
- DecimalFormat을 이용하면 숫자 데이터를 정수, 부동소수점, 금액 등의 다양한 형식으로 표현할 수 있다.
- 반대로 일정한 형식의 텍스트 데이터를 숫자로 쉽게 변환
- 형식화 클래스는 패턴을 정의하는 것이 전부다.
- DecimalFormat 사용법
  {{< highlight java >}}
double number = 1234567.89;

// 1.원하는 출력형식의 패턴을 작성하여 DecimalFormat 인스턴스를 생성
DecimalFormat df = new DecimalFormat("#.#E0"); 

// 2.출력하고자 하는 문자열로 format 메소드를 호출
String result = df.format(number);
{{< /highlight >}}

### 2.2. SimpleDateFormat
- 날짜 데이터를 원하는 형태로 다양하게 출력
- 사용방법
  - 원하는 출력 형식의 패턴을 작성하여 SimpleDateFormat 인스턴스를 생성
  - 출력하고자 하는 Date 인스턴스를 가지고 format(Date d)를 호출
    {{< highlight java >}}
Date today = new Date();
SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd");
String result = df.format(today);
//오늘 날짜를 yyyy-MM-dd 형태로 반환
{{< /highlight >}}
- `parse(String source)`를 사용하여 날짜 데이터의 출력형식 변환 가능
- SimpleDateFormat의 `parse(String source)`는 문자열 source를 날짜 Date 인스턴스로 변환해줌
    {{< highlight java >}}
DateFormat df = new SimpleDateFormat("yyyy년 MM월 dd일");
DateFormat df2 = new SimpleDateFormat("yyyy/MM/dd");
Date d = df.parse("2018년 10월 3일");

System.out.println(df2.format(d)); // 2018/10/03
{{< /highlight >}}

### 2.3. ChoiceFormat
- ChoiceFormat은 특정 범위에 속하는 값을 문자열로 변환
- 연속적/불연속적인 범위의 값 처리에 유용(예를 들어 90점까지 A, 80점까지 B, ..)
- 패턴을 사용할 경우 `limit#value` 형태로 사용
  - 구분자로 `#`는 경계값 포함, `<`는 미포함


## 3. java.time 패키지
Java의 탄생과 함께한 Date와 Calendar의 단점을 해소하기 위해  
JDK 1.8부터 `java.time 패키지`가 추가되었다.  
이 패키지는 4개의 하위 패키지가 있다.  

`java.time` - 날짜와 시간을 다루는데 필요한 핵심 클래스들 제공  
`java.time.chrono` - 표준(ISO)이 아닌 달력 시스템을 위한 클래스들 제공  
`java.time.format` - 날짜와 시간을 파싱, 형식화하기 위한 클래스들 제공  
`java.time.temporal` - 날짜와 시간의 필드와 단위(unit)을 위한 클래스들 제공  
`java.time.zone` - 시간대(time-zone)와 관련된 클래스들 제공

### 3.1 Immutable & Thread-safe
위 패키지의 클래스들은 String 클래스처럼 `Immutable`이다. 즉, 날짜나 시간을 변경하면 기존의 객체가 변경되는 것이 아니라, 새로운 객체를 반환한다.  
기존의 Calendar 클래스는 변경가능 하므로 멀티쓰레드 환경에서 안전하지 않았다.  

멀티쓰레드 환경에서는 여러 쓰레드가 동시에 같은 객체에 접근할 수 있어서  
변경 가능한 객체의 데이터가 잘못 될 가능성이 있다.  
이를 쓰레드 안전(Thread-safe)하지 못하다고 한다.

### 3.2. java.time 패키지의 핵심 클래스
시간을 표현할 때는 `LocalTime`,  
날짜를 표현할 때는 `LocalDate`,  
모두 표현할 때는 `LocalDateTime`,  
시간대(time-zone)까지 표현하려면 `ZonedDateTime` 사용

### 3.3. 타임스탬프(time-stamp)
날짜와 시간을 초단위로 표현한 값.  
이 값은 날짜와 시간을 하나의 정수로 표현할 수 있어서 날짜와 시간의 차이를 계산하거나 순서를 비교하는데 유리하다. (그래서 데이터베이스에서 많이 사용)

### 3.4. 객체 생성하기
java.time 패키지에 속한 클래스의 객체 생성은 `now()`와 `of()`로 한다.

- `now()` 예시
{{< highlight java>}}
LocalDate date = LocalDate.now();
LocalTime time = LocalTime.now();
{{< /highlight>}}
- `of()` 예시
{{< highlight java>}}
LocalDate date = LocalDate.of(2010, 10, 04); // 2010년 10월 4일(군입대ㅋ)
LocalTime time = LocalTime.of(23, 59, 59); // 23시 59분 59초
{{< /highlight >}}
  
### 3.5. 필드 값 변경하기
날짜와 시간에서 특정 필드 값을 변경하려면 `with`로 시작하는 메소드 사용

- 종류
{{< highlight java>}}
LocalDate withYear(int year)  
LocalDate withMonth(int month)  
LocalDate withDayOfMonth(int dayOfMonth)  
LocalDate withDayOfYear(int dayOfYear)  

LocalTime withHour(int hour)  
LocalTime withMinute(int minute)  
LocalTime withSecond(int second)  
LocalTime withNano(int nanoOfSecond)
{{< /highlight >}}
- `with()` 사용시 원하는 필드를 직접 지정 가능
{{< highlight java>}}
LocalDate with(TemporalField field, long newValue)
{{< /highlight >}}
- 필드를 변경하는 메소드들은 항상 새로운 객체를 생성해서 반환하므로 대입연산자를 같이 사용해야 한다.
{{< highlight java>}}
date = date.withYear(2018); // 년도를 2018년으로 변경
time = time.withHour(12); // 시간을 12시로 변경
{{< /highlight >}}
- LocalTime의 `truncatedTo()`는 지정된 것보다 작은 단위의 필드를 0으로 만든다.
{{< highlight java>}}
LocalTime time = LocalTime.of(12, 34, 56); // 12시 34분 56초
time = time.truncatedTo(ChronoUnit.HOURS); // 시(hour)보다 작은 단위를 0
System.out.println(time); // 12:00
{{< /highlight >}}

### 3.6. 날짜와 시간 비교
- `compareTo()`
{{< highlight java>}}
int result = date1.compareTo(date2);
// 같으면 0, date1이 이전이면 -1, 이후면 1
{{< /highlight >}}
- boolean형 메소드들
{{< highlight java>}}
boolean isAfter (ChronoLocalDate other)
boolean isBefore (ChronoLocalDate other)
boolean isEqual (ChronoLocalDate other)
{{< /highlight >}}

### 3.7. LocalDateTime
- `LocalDate`와 `LocalTime`을 합쳐서 `LocalDateTime`을 만들 수 있다.
  
    ~~~java
    LocalDate date = LocalDate.of(2018, 10, 05);
    LocalTime time = LocalTime.of(12,34,56);

    LocalDateTime dt = LocalDateTime.of(date, time);
    LocalDateTime dt2 = date.atTime(time);
    LocalDateTime dt3 = time.atDate(date);
    LocalDateTime dt4 = date.atTime(12, 34 ,56);
    LocalDateTime dt5 = time.atDate(LocalDate.of(2018, 10, 05));
    LocalDateTime dt6 = date.atStartOfDay(); // = date.atTime(0,0,0);

    LocalDateTime dateTime = LocalDateTime.of(2018, 10, 05, 12, 34, 56);
    LocalDateTime today = LocalDateTime.now();
    ~~~

- 반대로 `LocalDateTime`을 `LocalDate` 또는 `LocalTime`으로 변환할 수 있다.

    ~~~java
    LocalDateTime dt = LocalDateTime.of(2018, 10, 05, 12, 34, 56);
    LocalDate date = dt.toLocalDate();
    LocalTime time = dt.toLocalTime();
    ~~~

### 3.8. ZonedDateTime
>LocalDateTime에 타임존(tiem-zone)을 추가하면 ZonedDateTime이 된다.  
ZoneId는 일광 절약시간(DST, Daylight Saving Time)을 자동으로 처리해준다.  
LocalDateTime에 atZone()으로 시간대 정보를 추가하면, ZonedDateTime을 얻을 수 있다.

~~~java
ZoneId zid = ZoneId.of("Asia/Seoul");
ZonedDateTime zdt = dateTime.atZone(zid);
~~~

>특정 타임존의 시간 알기

~~~java
ZoneId nyId = ZoneId.of("America/New_York");
ZonedDateTime nyTime = ZonedDateTime.now().withZoneSameInstant(nyId);
// now() 대신 of() 사용하여 날짜&시간 지정 가능
~~~

>UTC로부터 얼마나 떨어져있는지를 ZoneOffSet으로 표현

### 3.9. OffsetDateTime  
>`ZonedDateTime`은 ZoneId로 구역을 표현하는데,    
ZoneOffset을 사용하는 것이 `OffsetDateTime` 이다.  
ZoneId는 일광절약시간 처럼 타임존과 관련된 규칙들을 포함하지만,  
ZoneOffset은 단지 시간대를 시간의 차이로만 구분

### 3.10. Period와 Duration  
>두 날짜의 차이를 나타내는 Period는 `between()`으로 얻을 수 있다.

~~~java
LocalDate date1 = LocalDate.of(2017, 1, 16); // 첫 직장 입사일ㅋㅋ
LocalDate date2 = LocalDate.of(2018, 6, 4); // 두번째 직장 입사일

Period pe = Period.between(date1, date2);
// date1이 date2보다 이전이면 양수, 이후면 음수로 Period에 저장
~~~

>`until()` 은 `between()`과 같은 역할이지만,  
`between()`은 static 메소드이고, `until()`은 인스턴스 메소드이다.  
D-day를 구하는 경우, 두 개의 매개변수를 받는 `until()`을 사용하는 것이 낫다.

~~~java
long sec = LocalTime.now().until(endTime, ChronoUnit.SECONDS);
~~~

>LocalDate의 `toEpochDay()` 는 Epoch Day인 `1970-01-01`부터 날짜를 세어서 반환.  
Period를 사용하지 않고 두 날짜 사이의 일 수를 편하게 계산할 수 있다.
 
~~~java
LocalDate date1 = LocalDate.of(2010, 08, 20); // 사귀기 시작한 날
LocalDate date2 = LocalDate.of(2017, 11, 25); // 결혼한 날ㅎ

long 연애기간 = date2.toEpochDay() - date1.toEpochDay();
~~~

### 3.11. 출력형식 정의
>DateTimeFormatter의 ofPattern()으로 원하는 출력형식을 작성할 수 있다.

~~~java
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy/MM/dd");
~~~