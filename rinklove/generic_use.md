## 상한 경계 와일드카드와 하한 경걔 와일드카드의 사용 방법

### 지난 시간 요약
1. 제네릭은 클래스 레벨에서 상속할 수 있음 **```public class Box<? extends List>```**
2. 제네릭은 메서드 정의부에서 와일드 카드를 사용하여 무공변성의 성질을 공변화할 수 있음.(**super, extends** 키워드 사용 가능)

### 그래서 <? super Type>, <? extends Type>는 언제 사용?

우선 Oracle에서는 이를 다음과 같이 말한다. - https://docs.oracle.com/javase/tutorial/java/generics/wildcardGuidelines.html
```

```

> 제네릭을 활용한 프로그래밍을 배울 때, 유독 헷갈리는 개념 중 하나가 상한 경게 와일드카드와 와 하한 경게 와일드 카드를 언제 사용해야하는 지 결정하는 것 입니다.<br>
> 이를 위해서 위해서, 여러분들은 변수가 두 가지(in, out) 중 하나의 기능을 제공한다고 생각하는 것이 도움이 됩니다.<br>
> **An "In" Variable**<br>
> "in" variable은 코드에 데이터를 제공합니다. src, dest 매개변수를 받는 copy() 메서드를 상상해보세요.**copy(src, dest)**<br> src인자는 카피될 데이터를 제공하는데, 이 src 변수가 "in" Variable이 됩니다.<br>
> **An "Out" Variable**<br>
> "out" variable은 다른 곳에서 사용할 데이터를 보관합니다. copy(src, dest) 메서드에서 dest인자는 데이터에 접근하기 때문에 "out" Variable이 됩니다.<br>
> 물론 일부 변수는 "in"과 "out"의 기능을 동시에 할 수 있습니다.


