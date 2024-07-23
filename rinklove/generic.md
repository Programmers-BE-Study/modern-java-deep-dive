## Generic
> Java 5부터 나온 개념으로 “데이터의 타입을 일반화(Generalize)한다”는 의미.<br>
> 제네릭은 **클래스나 메소드에서 사용할 내부 데이터 타입을 컴파일 시에 미리 지정**

### 사용법
아래 코드처럼. 겉화살괄호 사이에 참조 자료타입을 작성.
<br>
```
  List<String> wordLog = new ArrayList<>();
```
<br>

## Generic개념 등장 전(Java 5 이전)
 - 모든 객체를 담기 위해서 필드를 Object Type으로 선언 
 - 객체를 저장하는 set() 메서드와 저장한 객체를 반환하는 get()메서드를 구현.
```
  public class Box<T> {
    private T object;

    public void set(T object) {
        this.object = object;
    }

    public T get() {
        return object;
    }
}
```

### 문제점
`get() 메서드를 사용하면 Object 클래스가 나오기 때문에 필요할 때 마다 강제 형변환 작업 수행 필요`

### Quiz
Object 클래스의 toString() 메서드 구현부
```
public class Object {
//중략
  public String toString() {
        return getClass().getName() + "@" + Integer.toHexString(hashCode());
    }
//후략
}
```
<hr>
main() 메서드 실행부

```
public static void main(String[] args) {
        List list = new LinkedList();
        list.add(new String("안녕하세요"));
        Object element = list.iterator().next();
        /*
            String Type 객체를 넣었지만,
            값을 꺼낼때는 Object Type 객체 반환
        */
        System.out.println(element); //?
    }
```
> 만약 String객체가 아닌 다른 객체였다면?<br>
> 해당 객체를 사용하기 위해 강제 형변환이 필요했을 것이다.

→ `자주 사용 시, 프로그램의 성능 저하

<br>

## 제네릭 개념 등장 후(Java 5 ~ )
- 미리 타입을 선언함으로써 타입 변환을 할 필요가 없어짐.
![after](https://github.com/user-attachments/assets/8ec05e30-bced-485b-ba63-d6092c0b5fea)<br>
- Box 클래스에 정의하지 않은 클래스를 넣을 시 컴파일 에러 발생 → **프로그램 안정성 강화**<br>
![img](https://github.com/user-attachments/assets/ab5d558a-2af6-4b85-a99a-5ca88270c1b8)


## 요약
**1. Generic은 클래스나 메소드에서 사용할 내부 데이터 타입을 컴파일 시에 미리 지정**<br>
**2. 사용 시 프로그램의 컴파일 단계에서 에러를 잡아내어 안정성을 강화할 수 있음.**

## References
https://docs.oracle.com/javase/tutorial/extra/generics/intro.html<br>
https://www.freeblog-web.info/post/74?blogId=1
