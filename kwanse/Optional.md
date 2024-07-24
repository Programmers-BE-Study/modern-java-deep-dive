# Optional 의 등장 배경과 주의 사항

## 등장 배경 (Optional 사용 이유)
우리는 항상 NullPointerException 과 가까이 있다. 조금만 신경을 안써도 NPE 가 나타날 수 있다.



예를 들어

```java
public class Main {

    public static void main(String args[]) throws Exception {
        
        Member member = new Member();
        String name = member.getBoard().getTitle; // Board 가 없다면??
    }
}
```

getBoard() 에서 Board 객체가 없을 경우 NullPointerException 가 발생하게 된다.



그렇다면 이 문제를 어떻게 해결해야 할까?





## 해결 방법 (자바 8 이전)
```java
public class Main {

    public static void main(String args[]) throws Exception {

        Member member = new Member();
        Board board = member.getBoard();
        if (board != null) {
            String name = board.getTitle();
        }
    }
}
```


NullPointerException 이 발생할 것으로 예상되는 곳에 항상 if 체크를 해줘야 한다.



하지만 이런 간단한 경우가 아닌 조금만 더 복잡해져도 null 체크를 위한 코드 때문에 가독성이 크게 저해된다.

또한 실수로 인해 빼먹은 경우도 당연히 생길 수 있다.





## Optional 의 등장
Optional 로 null 이 될 가능성이 있는 객체를 감싸줌으로써 이러한 문제들을 해결할 수 있게 되었다.



Optional 을 사용함으로써 null 이 될 가능성이 있는 객체를 직접 다루지 않아도 된다.

또한 null 체크를 직접 하지 않아도 된다.

그리고 해당 객체가 null 일 수 있다는 가능성을 표현할 수 있다.





## Optional 사용법
1. 해당 객체가 null 이 될 가능성이 '존재' 하는 경우



`Optional<Member> o = Optional.ofNullable(null);`

`Optional<Member> o = Optional.ofNullable(member);`



ofNullale() 을 사용하여 Optional 에 값을 넣어줄 수 있다.





2. 해당 객체가 null 이 될 가능성이 '전혀' 없는 경우



`Optional<Member> o = Optional.of(member);`



of() 를 사용하여 Optional 에 값을 넣어줄 수 있으나 값이 null 일 경우 NullPointerException 이 발생한다.





3. 그냥 빈 Optional 을 가지고 싶은 경우



`Optional<Member> o = Optional.empty();`



empty() 를 사용하여 Optional 에 비어있는 값을 넣어줄 수 있다.

Optional.empty() 는 **new Optional<>(null);** 를 반환한다.





4. Optional 이 담고 있는 객체 꺼내고 싶은 경우 && Optional 이 비어있지 않은 경우



`Member member = o.get(); // Optional<Member> o`



하지만 get() 에는 큰 문제점이 있다.

Optional 이 비어있지 않아야 한다는 조건이다.



get() 으로 비어있는 Optional 에 접근하는 경우 NoSuchElementException 을 던진다.



이렇게 되면 결국 Optional o 가 비어있는지 안비어있는지 또 체크를 해야하고 Optional 을 사용하는 이유가 완벽하게 사라진다.

null 을 처리하지 못하는 Optional 을 사용하는 것은 가독성과 우리의 손목건강만 더 나쁘게 할 뿐이라고 생각한다..





5. get() + 비어있는 경우 넘어온 인자 반환



`Member member = o.orElse(new Member());`



orElse(param) 는 value!=null>value:param  을 리턴한다.

=> 평소에는 get() 처럼 동작하나 Optional 이 비어있을 경우 param 을 반환한다.





6. get() + 비어있는 경우 해당 메서드 실행



orElseGet()

```java
public class Member {
    Member member1 = o.orElseGet(() -> {
        System.out.println("no member");
        return new Member();
    });
}
```

orElse() 와 orElseGet() 의 차이점
orElse() 의 인자로 '메서드' 를 넣어버리면 값이 비어있든 안비어있든 해당 메서드가 호출되어 버린다.



orElseGet() 의 경우에는 값이 비어있을 경우에 param.get() 을 반환하기 때문에 값이 비어있을 경우에만 메서드가 호출되게 된다.





Optional 사용 시 주의점
결국 우리가 Optional 을 사용하는 이유는 null 을 쉽게 다루기 위해서이다.

이러한 Optional 의 본분을 잊고 코드를 작성하게 된다면 오히려 Optional 을 안쓰는게 나은 코드가 작성될 확률이 높다.



1. Optional 은 리턴값으로만 쓰기를 권장한다.



어째서일까? orElse() 로 Optional 이 비어있는 경우를 체크해주면 해결되는 일 아닐까?


```java
import java.util.Optional;
public class Member {

    public Board getBoard(Optional<Board> board) {
        return board.orElse(new Board());
    }
}
public class Main {

    public static void main(String args[]) throws Exception {

        Member member = new Member();
        Board board = member.getBoard(null);
    }
}
```

이렇게 해버리는 경우 확실하게 NullPointerException 이 터져버린다.



getBoard() 에서 board.orElse 를 하기 때문에

인자로 null 이 넘어와버린다면 문법적으로는 허락되나 NPE 가 던져진다.





2. Optional 을 리턴하는 메서드에서 null 을 리턴하지 말자

Optional 을 사용하기로 하였으면 null 을 쓰지 말고 Optional 을 이용해서 처리하자.



3. Container type 을 Optional 로 감싸지 말자

Container type 은 arrays, collection 등을 포함한다.



Container type 은 비어있음을 스스로 표현할 수 있는 타입이다.

따라서 굳이 Optional 을 쓸 이유가 없고 Optional 을 쓰게 된다면 불필요한 포장이 되어버린다.

 