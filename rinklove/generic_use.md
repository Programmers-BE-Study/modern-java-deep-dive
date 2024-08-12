## 상한 경계 와일드카드와 하한 경계 와일드카드의 사용 시기

### 지난 시간 요약
1. 제네릭은 클래스 레벨에서 상속할 수 있음 **```public class Box<? extends List>```**
2. 제네릭은 메서드 정의부에서 와일드 카드를 사용하여 무공변성의 성질을 공변화할 수 있음.(**super, extends** 키워드 사용 가능)

### 그래서 <? super Type>, <? extends Type>는 언제 사용?

우선 **```Oracle 공식문서```**에서는 이를 다음과 같이 말한다(본인이 이해하기 쉽게 약간 변형). - https://docs.oracle.com/javase/tutorial/java/generics/wildcardGuidelines.html
```
public static Path copy(Path source, Path target, CopyOption... options)
        throws IOException
    {
        FileSystemProvider provider = provider(source);
        if (provider(target) == provider) {
            // same provider
            provider.copy(source, target, options);
        } else {
            // different providers
            CopyMoveHelper.copyToForeignTarget(source, target, options);
        }
        return target;
    }
```

> 제네릭을 활용한 프로그래밍을 배울 때, 유독 헷갈리는 개념 중 하나가 상한 경게 와일드카드와 와 하한 경게 와일드 카드를 언제 사용해야하는 지 결정하는 것 입니다.<br>
> 이를 위해서 위해서, 여러분들은 변수가 두 가지(in, out) 중 하나의 기능을 제공한다고 생각하는 것이 도움이 됩니다.<br>
> **An "In" Variable**<br>
> "in" variable은 코드에 데이터를 제공합니다. 위의 Files.copy() 메서드를 상상해보세요.<br> source인자는 카피될 데이터를 제공하는데, 이 source 변수가 "in" Variable이 됩니다.<br>
> **An "Out" Variable**<br>
> "out" variable은 다른 곳에서 사용할 데이터를 보관합니다. copy() 메서드에서 target 인자는 데이터를 받아들이기 때문에 "out" Variable이 됩니다.<br>
> 물론 일부 변수는 "in"과 "out"의 기능을 동시에 할 수 있습니다.


이 부분을 Effective Java 3에서는 **```PECS 공식```** 이라는 표현으로 작성되어있음.
> - 외부에서 온 데이터를 **생산(```P```roducer)** 한다면 <? ```e```xtends T> 를 사용 (하위타입으로 제한)
> - 외부에서 온 데이터를 **소비(```C```onsumer)** 한다면 <? ```s```uper T> 를 사용 (상위타입으로 제한)

### Example
- CustomList의 생성자에서 매개변수로 받는 collections에는 필드변수인 animals에 카피할 데이터를 제공하므로 **"In Variable"**임.
- clone()의 매개변수인 destination은 animals에 저장된 데이터를 복사해서 clone()를 호출한 메서드로 이동하므로, **"Out Variable"**임
```
interface Animal {}
class Dog implements Animal {}
class Cat implements Animal {}
class Lamb implements Animal {}

class CustomList<T> {
    Object[] animals;

    CustomList(Collection<? extends T> collections) {
        animals = new Object[collections.size()];
        int index = 0;
        for (T element : collections) {
            animals[index++] = element;
        }
    }

    public void clone(Collection<? super T> destination) {
        for (Object animal : animals) {
            destination.add((T) animal);
        }
    }
}

public class Main {
    public static void main(String[] args) {
        //랜덤 동물 인스턴스 생성 후 CustomList에 저장
        List<Animal> animals = getRandomList();

        CustomList<Animal> customList = new CustomList<>(animals);
        for (Object animal : customList.animals) {
            System.out.println(animal.getClass().getSimpleName());
        }

        ArrayList<Animal> dest = new ArrayList<>();
        customList.clone(dest);

        //custom리스트에 있는 인스턴스를 클론
        System.out.println();
        for (Animal animal : dest) {
            //clone해온 객체를 가지고 작업 가능
        }
    }

    private static List<Animal> getRandomList() {
        List<Animal> list = new ArrayList<>();
        for(int i = 0; i < 6; i++) {
            Animal[] select = {new Cat(), new Dog(), new Lamb()};
            int random_index = (int)(Math.random() * 3);
            list.add(select[random_index]);
        }
        return list;
    }
}
```

### 요약
- 하한 경계 와일드카드는 다양한 하위 클래스 타입의 인스턴스를 복사(저장)하는 데 사용.
- 상한 경계 와일드카드는 복사된 인스턴스를 외부에서 사용하기 위해 사용.
