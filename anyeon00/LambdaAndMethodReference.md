## Lambda Expression

<strong> Functional Interface >> Anonymous Object >> Lambda Expression </strong><br>

```java
ex)
MyInterface mi = new MyInterface(){
	public void remote(TV tv){
		System.out.println("play " + tv.channel());
	}
}
```
인터페이스명 제거, 함수 헤더 제거, 파라미터 타입 제거
-> 파라미터 이름와 함수바디만 남음
```java
(tv) { System.out.println("play " + tv.channel());
```
이것을 다음과 같이 표현
```java
MyInterface mi  = tv -> "play" + tv.channel();
```

- 주로 사용되는 예
  Arrays.sort()에서 Comparator Override
```java
ex) Arrays.sort(students, (o1, o2) -> o1.getNum() - o2.getNum());
```


## Method Reference
최종적으로 적용될 메소드의 레퍼런스를 지정해주는 표현 방식

### Method Reference의 4가지 타입
1. 인스턴스 메소드 레퍼런스 (InstanceType::instanceMethodName) <br>
   매개변수가 메소드의 Receiver 객체로 사용됨 <br>ex)
   ```java
   MyInterface mi = String::length;
   // (String s) -> s.length() 로 사용됨
   ```

2. 특정 객체의 인스턴스 메소드 레퍼런스 (ClassName::instanceMethodName) <br>
   매개변수가 멤버함수의 매개변수로 전달됨 <br>ex)
   ```java
   MyInterface mi = System.out::println
   // (x) -> System.out.println(x)로 사용됨
   ```

3. 스태틱 메서드 레퍼런스 (ClassName::staticMethodName) <br>
   매개변수가 스태틱 메소드의 매개변수로 사용됨 <br>ex)
   ```java
   MyInterface mi = String::valueOf;
   // (input) -> String.valueOf(input) 으로 사용됨
   ```

4. 생성자 레퍼런스 (ClassName::new) <br>
   매개변수가 생성자의 매개변수로 전달 됨 <br>ex)
   ```java
   Supplier<MyObject> supplier = MyObject::new;
   // () -> new MyObject()
   ```

### 얻는 이점
- Lambda Expression과 같은 맥락으로, 표현이 간단해짐
- 입력값을 변경하지 말라는 표현방식이기도 함
- 개발자의 개입을 차단함으로써 안정성을 얻을 수 있음
