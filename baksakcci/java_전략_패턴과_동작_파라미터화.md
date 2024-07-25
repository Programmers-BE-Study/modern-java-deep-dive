# 0.
사과가 담긴 `inventory`를 조건에 맞게 필터링해서 List로 반환하는 로직입니다.

```java
public List<Apple> filterApples(List<Apple> inventory) {  
    List<Apple> result = new ArrayList<>();   
    for (Apple apple : inventory) {        
	    if (Color.GREEN == apple.getColor()) {   --> 조건이 추가되거나 바뀌면 직접 context 코드 수정         
		    result1.add(apple);        
		}        
		OR    
		if (apple.getWeight() > 150) {            
			result2.add(apple);       
		} 
	}
    return result;  
}

// client
appleFilter.filterApples(inventory);
```

`filterApples()`의 조건이 수정되면 직접 코드를 수정해야 하는 상황이 발생합니다.
그래서 유지보수성을 높이고자 context 코드의 수정을 방지하기 위해서 전략 패턴을 도입합니다.

> ### 이해 관계
> client, context, strategy 3명의 이해관계가 등장한다.
> `client`는 context 메서드를 사용하는 주체이고, `context`는 `client`에게 요청을 받은 객체이다.

# 1.
> ### 전략 패턴
> context에서 알고리즘을 선택할 필요가 없는, 즉 코드를 수정할 필요 없는 유연한 설계를 얻을 수 있습니다.

```java
// strategy interface
public interface AppleFilterStrategy {  
  
    boolean test(Apple apple);  
}

// implements class
public class WeightFilterStrategy implements AppleFilterStrategy {  
  
    public boolean test(Apple apple) {  
        return apple.getWeight() > 150;  
    }  
}

// context code
```java
public List<Apple> filterApples(List<Apple> inventory) {  
    List<Apple> result = new ArrayList<>();   
    for (Apple apple : inventory) {        
	    if (strategy.test(apple)) { --> 수정하지 않아도 외부 전략에 의해 수정되는 효과!  
		    result.add(apple);  
		}
	}
    return result;  
}

// client
AppleFilterStrategy strategy = new WeightFilterStrategy();  
  
appleFilter.filterApples(inventory, strategy);
```

1. 다양한 전략 클래스들을 만든 후,
2. Client 코드에서 전략을 생성하고 메서드에 주입합니다.

이제 `filterApples()`는 내부에서 직접 코드를 고치지 않고도 전략을 수정할 수 있습니다!

이렇게 매개변수로 값이 아닌 '동작', '행위'를 전달하는 것을 '동작 파라미터'라고 합니다.

> ### 동작 파라미터
> 메서드의 동작을 파라미터로 전달하는 것
> 종류: 클래스, 익명 클래스, 람다

> ❗전략 패턴을 구현한 클래스도 동작 파라미터다
>
# 2.
> [!tip] 익명 클래스 사용
> 익명 클래스를 사용하면 전략 클래스들을 일일이 구현하지 않고도 바로 적용할 수 있습니다.

```java
appleFilter.filterApples(inventory, new AppleFilterStrategy() {  
            @Override  
            public boolean test(Apple apple) {  
                return apple.getWeight() > 150;  
            }  
        });
```

이제 전략 클래스를 일일이 생성하지 않아도, 실행 시점에 동적으로 클래스가 생성되어 전달됩니다.

# 3.
> ### 람다 사용
> 람다를 사용하면 익명 클래스 보다 훨씬 간결한 코드를 작성할 수 있습니다.

```java
// client
appleFilter.filterApples(inventory, apple -> Color.GREEN == apple.getColor());
appleFilter.filterApples(inventory, apple -> apple.getWeight() > 150);
...
```

하지만 Spring project를 진행할 때는, 보통 전략이 많이 생성되진 않기 때문에 클래스로 만들어 전략을 주입하는 방법을 많이 사용하는 듯 합니다!

추가로, 전략 패턴을 응용한 템플릿 콜백 패턴은 동작 파라미터를 적극적으로 활용하기 때문에, 익명 클래스 혹은 람다 방식을 많이 활용하는 것 같습니다.
대표적으로 `RestTemplate`, `JdbcTemplate` 등이 있습니다.

# 4.
> ### Stream으로의 확장
> Stream에서 동작 파라미터를 적극 활용합니다.

Java 8 버전에서 새로 추가된 Stream API에서도 전략 패턴을 사용하는 모습을 볼 수 있습니다.

Stream에 추가된 함수형 인터페이스인 `Predicate`의 내부를 보면 다음과 같습니다.

```java
@FunctionalInterface  
public interface Predicate<T> {  

	// abstract method
    boolean test(T t);  

	// default, static method
	...
}

// client
List<Integer> collect = strings.stream()  
        .map(Integer::parseInt)  
        .filter(n -> n == 1)  
        .collect(Collectors.toUnmodifiableList());
```

위에서 나온 `Strategy` interface와 매우 유사한 형태입니다. 더불어 제네릭까지 추가하여 유연성을 더했습니다.

Client는 이를 `filter()`함수에 람다 식을 파라미터에 적용하여 필터링 작업을 할 수 있습니다.

람다 식 `n -> n == 1`은 결국 '전략'입니다.
`Stream`은 컬렉션들을 어떻게 필터링하고, 변환하고, 재조합할지에 대한 '전략'을 람다 형식으로 적용할 수 있게 하는 API 입니다.

> ❗ 만약 Stream API에서 람다가 없었다면, 익명 클래스가 없었다면...
> filter나 map 매개변수로 **일일이 전략 클래스를 추가**해야 하는 대참사가 발생할 수 있습니다..


# 정리
- 전략 패턴을 사용하면 코드 수정이 필요 없는 유연한 설계를 가져올 수 있다.
- 패턴을 학습할 땐 이해 관계를 잘 파악하자.
- 동작 파라미터는 메서드의 동작을 변수로 저장하고 파라미터로 전달할 수 있다.
- 람다를 사용하면 동작 파라미터를 훨씬 간결하게 작성할 수 있다.
- Stream API 또한 파라미터에 전략을 넣어서 결과를 얻는 구조이다.

결국 온 세상이 전략 패턴입니다.

# 참조
모던 자바 인 액션 1, 2장

[[모던 자바] 동작 파라미터화란 무엇인가? - beekei](https://devbksheen.tistory.com/entry/%EB%AA%A8%EB%8D%98-%EC%9E%90%EB%B0%94-%EB%8F%99%EC%9E%91-%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0%ED%99%94%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)

[전략 패턴 - guru](https://refactoring.guru/ko/design-patterns/strategy)