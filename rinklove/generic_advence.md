# 제네릭 심화
**1. 제네릭 상속 - extends**<br>
**2. 와일드 카드 - ?**

<br>

## 1. 제네릭 상속
### 배경
앞에서 제네릭 문법을 적용한 Box클래스를 생각해보자.<br>
Box 인스턴스를 생성할 때 정의할 수 있는 파라미터는 모든 타입이 가능.<br><br>
그렇다면 실제로는 이렇게 쓰일 리는 없지만, 이런 코드도 컴파일 에러 없이 정상적으로 수행이 가능하다.
```
public static void main(String[] args) {
    Box<Map<String, List<Box>>> box = new Box<>();
}
```
**이 코드에서 느낀 문제점**
중첩된 제네릭의 사용으로 가독성 저하 + box 인스턴스의 명확하지 않은 역할.
→ 제네릭의 원래 사용 의도와도 많이 다를 뿐더러, 개발자 입장에서도 사용하기 버거울 것임.

### 제네릭 상속 - extends
그렇다면 이러한 문제를 해결하기 위한 방법이 바로 **클래스 단위에서 정의할 수 있는 제네릭 타입 파라미터를 상속으로 제한**하는 것이다.<br>
이번엔 Box에 선언할 수 있는 타입 파라미터를 Number 클래스를 상속받은 클래스로 제한했다.
```
public class Box<T extends Number> {
    private T object;
    public void set(T object) {
        this.object = object;
    }
    public T get() {
        return object;
    }

    public static void main(String[] args) {
        Box<Map<String, List<Box>>> box = new Box<>();  //compile Error!
        Box<Integer> integerBox = new Box<>();
        Box<Long> longBox = new Box<>();
        Box<Boolean> booleanBox = new Box<>();  //compile Error!
        Box<String> stringBox = new Box<>();    //compile Error!
    }
}
```
제네릭을 상속으로 제한함으로써 기존에 역할이 명확하지 않았던 Box클래스가 숫자 객체를 저장하는 클래스로 역할이 명확해졌다.

<br>

## 2. 와일드 카드 - ?
### 와일드 카드의 등장 배경
> 제네릭의 기본 성질인 무공변성 때문에 와일드 카드의 개념이 생기게 되었다.

### 공변성, 반공변성, 무공변성
- 공변성: 리스코프 치환 원칙(LSP)과 동일, 제네릭 구조가 클래스 구조를 따라감.
> LSP: 부모 객체와 자식 객체가 있을 때 부모 객체를 호출하는 동작에서 자식 객체가 부모 객체를 완전히 대체할 수 있다는 원칙<br>
<img width="299" alt="image" src="https://github.com/user-attachments/assets/7d5b60e1-b6ec-4258-a860-3b2290a31da9"><br>
```
Parent p = new Child();
Parent[] parentArr = new Child[10];
```
- 반공변성: 제네릭 구조와 클래스 구조가 반대인 성질(공변성과 반대되는 성질)
> <img width="301" alt="image" src="https://github.com/user-attachments/assets/3a7cf375-1a90-47da-9087-5a8501a488ba">
```
Child c = new Parent(); //Complie Error!
```
- 무공변성: 클래스 구조와 관계 없이, 전달받은 타입으로만 받는 성질

### Quiz
```
public class Parent {

}

class Child extends Parent{

}

class QuizBox<T extends Parent> {

}

class GenericMain {
    public static void main(String[] args) {
        QuizBox<Parent> p1 = new QuizBox<Parent>(); //(1)
        QuizBox<Parent> p2 = new QuizBox<Child>();  //(2)
        QuizBox<Child> p3  = new QuizBox<Parent>(); //(3)
        QuizBox<Child> p4 = new QuizBox<Child>();   //(4)
        
        List<Parent> parents1 = new ArrayList<>();
        List<Child> children1 = new ArrayList<>();
        List<Parent> parents2 = new ArrayList<>();
        List<Child> children2 = new ArrayList<>();
        List<QuizBox<Parent>> testBoxes = new ArrayList<QuizBox<Child>>();  //(5)
        parents1 = children1; //(6)
        children2 = parents2; //(7)
    }
}
```

### 제네릭의 무공변성으로 인한 문제점
<img width="358" alt="image" src="https://github.com/user-attachments/assets/7d30fccc-329e-4033-a6b8-78fe53e26208">
<img width="330" alt="image" src="https://github.com/user-attachments/assets/f3898f1b-ce26-4343-9b4c-0be17b795923">

getList() 메서드에서 ```List<Object>```타입의 객체를 받겠다고 했는데, 메인 메서드에서 ```List<String>```타입 객체를 전달하면 컴파일 에러.
→ getList 오버로딩

⇢ 제네릭 타입이 다른 리스트를 매개변수로 전달할 때마다 getList()를 무한 오버로딩
⇢ 객체지향스러운 설계x



## References
https://grey920.github.io/server/java-covariant/ (공변성, 반공변성)
