본문에 들어가기에 앞서, 이 개념에 대해 서로 의견이 분분하다. 블로킹과 논블로킹은 명확하게 정의 및 구분할 수 있는데, 동기 비동기는 추상적인 개념이라 어떤 상황에서 해석하느냐에 따라 다르다. 무엇이 정답이라고 할 순 없기 때문에 너무 구분하려고 하지 말고 이 개념에 얽매이지 말자.

챕터마다 시퀀스 다이어그램이 등장할 것이다. 시퀀스 다이어그램을 보면서, 제어의 흐름이 어디로 가 있는지 쫒아간다면 이해하기 한결 수월하다.

본 내용은 우테코 10분 테코톡에 나온 출처 및 gpt를 이용했다.

--- 

우선 상대적으로 쉽게 이해할 수 있는 `blocking`과 `non-blocking`에 대해 알아보자.

> 제어할 수 없는 방법에 대한 처리 방법

무슨 소린지 모르겠으면 밑에 문장을 보자.

> 기다리는 것에 중점을 둔다.

그래도 아직까지 머릿속에서 그려지지 않는다.
일단 이 두 문장을 기억하며 먼저 `blocking`을 살펴보자.

![스크린샷 2024-08-01 112523](https://github.com/user-attachments/assets/ecf02988-4073-4f35-839a-96e253e6718d)

두개의 코드가 있고, 오른쪽에 시퀀스 다이어그램처럼 화살표를 표시했다.
코드가 실행하는 시간 순서대로 화살표가 이동하는 것을 볼 수 있다. 이를 제어권의 흐름이라 한다.

`사용자()`는 처음 메서드 블럭에 들어서서 `methodA()`를 만났다.
그래서 `methodA()`에게 '제어권'을 넘겨준다.
이 때, `사용자()`는 `methodA()`가 끝나고 결과를 돌려줄 때 까지 다음 코드를 실행하지 않고 기다린다. 
이를 `blocking` 상태라 한다.

![image](https://github.com/user-attachments/assets/ea4e9325-1359-48a8-9246-664dc9c1dee6)

`methodA()`가 결과를 반환하고 제어권을 `사용자()`에게 다시 넘겨준다.
`사용자()`는 그제서야 다음 코드를 실행하러 간다.

이번에는 `non-blocking`의 순서도이다. 
![image](https://github.com/user-attachments/assets/8d76e05d-5800-4375-b05d-7a46ae5d82fe)

이전의 `blocking` 방식과 다르게, `사용자()`는 `methodA()`를 실행시키고 바로 자기 할 일 하러 간다. 제어권은 `사용자()`가 계속 가지고 있으며 다음 코드를 실행시킨다.

이와 동시에 `methodA()`가 실행되어 2개의 코드 블럭이 동시에 실행된다. 제어권이 2개로 나눠진 셈이다.

하지만 여기서 이상한 점이 있다. `methodA()`의 결과는 어디로 가야 할까?
이미 `사용자()`는 결과에 관심이 없는 듯, 다음 코드를 실행시키러 떠났다.
일단 이런 의문을 가진 상태로 다음 스텝인 `동기`와 `비동기`를 알아보자.

---

동기는 영어로 `synchronous`이다. 이들을 쪼개보면, `syn`과 `chrono`로 나눠지는데 각각 '함께', '시간'이란 뜻을 가진다.

어원의 뜻에 맞게 해석해보면,
> 동기는 데이터를 같은 시간에 동일하게 맞춰주는 것이다.

데이터베이스의 동기화를 생각해보자. 데이터베이스는 어느 누구든지 같은 시간에 똑같은 데이터를 가져올 수 있도록 보장해야 한다.

우리는 `SELECT`문을 보내면서 요청에 맞는 컬럼 정보를 받기를 기대하고 있다.
우리는 `INSERT`문을 보내면서 요청이 잘 이루어졌는지 대답을 기대하고 있다.
![image](https://github.com/user-attachments/assets/42e1a97d-b501-4560-afed-38acb1d9e100)

중요한 것은
> 무언갈 받기를 기대한다
는 점이다.

이 점을 기억하며 계속 나아가보자.

`동기`라는 것은 2가지 관점이 존재한다.
![image](https://github.com/user-attachments/assets/c060bd1b-2a42-4325-98d7-b0cb0e3887d8)

1. `funA()`의 끝 시점과 `funB()`의 시작 시점을 일치시키기.
2. 제어권의 반환과 결과값의 전달 시점을 일치시키기.

`비동기`는 해당 그림의 반대라 생각하면 된다.
![image](https://github.com/user-attachments/assets/ac2e1cee-efdb-420b-99b8-472d321b9eaf)

1. `funA()`와 `funB()`의 시작, 끝 지점이 일치되는게 없다. 즉, 각자 실행한다.
2. 제어권을 '먼저' 반환하고 결과값을 나중에 전달한다.

자, 아까전에 `non-blocking`설명하면서 "결과값은 어디로 가야 되는지" 아직 해결하지 못했었다.
이제 그 의문을 해결하러 가보자.

---

다음으로 `sync + non-blocking`의 예시이다.

Linux의 IO 통신에 대한 시퀀스 다이어그램이다.
![image](https://github.com/user-attachments/assets/e1c02f6e-7c2c-409b-878c-54d3709501f9)

application은 kernel에게 IO에 대한 요청을 보내면, kernel에서는 read I/O 작업을 진행하기 시작한다. 그리고 바로 `EAGAIN`이라는 결과 값을 보낸다.
이것은 비동기일까?

아까전의 데이터베이스 예시를 대입해보자.
Application이 개발자고, kernel이 DB라고 가정했을 때, 개발자는 쿼리를 보낸 결과가 와야 처리할 수 있기 때문에 아무것도 못한다.

하지만 DB가 일찍 제어권을 반환해버렸다. 제어권을 먼저 반환했으니 `non-blocking`이다. 이 때 자기 할 일을 할 순 있긴 하다.

우리는 요청에 대한 결과값을 받아야 한다. 하지만 DB는 "아직 응답이 완료되지 않았다."는 메세지만 전달한다. 
여기서 중요하다.
그 메세지를 우리가 전달 받았다. 그 메세지도 결과 값일까? 여기서는 결과 값이라고 본다. 그래서 개발자가 원치 않은 응답이라도 결과 값을 받았다고 간주하기 때문에, 제어권과 결과값을 둘 다 동일한 시간대에 받아서 `sync` 방식이라 부른다.

결과값을 받기 위해 개발자는 DB에게 작업이 끝났는지 계속 물어봐야 한다. 
집념을 가지고 끝까지 물어본 끝에 DB가 처리된 정보를 개발자에게 전달했다. 그래서 제어권과 결과값을 같은 시간에 넘겨주었기 때문에 `sync`방식이라 한다.
![image](https://github.com/user-attachments/assets/89c464ba-5937-4252-bbba-6651944b58e5)


실제 코드 예시를 살펴보자.

```java
int number = 5;  
  
ExecutorService executorService = Executors.newCachedThreadPool();  
Future<Integer> futureTask = executorService.submit(() -> factorial(number)); // non-blocking 

int cnt = 0;  
while (!futureTask.isDone()) {  // sync
    // async  
    cnt += 1;  
    System.out.println("future task is not finished yet... " + cnt);  
}  
int result = futureTask.get(); // blocking, sync  
  
assertThat(result).isEqualTo(120);  
  
executorService.shutdown();
```

java 5에서 나온 `Future`라는 기능을 살펴보자.
반복문을 통해서 작업이 끝났는지 물어보는데, 이 부분은 `sync` 방식이지만, 그 안에 작업이 있다면 그것은 `async` 방식이다.
또, `blocking`작업이 있을 수 있고 `non-blocking`작업이 있을 수도 있다.

즉, 해당 코드는 그저 `non-blocking`과 `sync`만 있지 않고, 동기 비동기가 섞여 있고, 블로킹 논블로킹이 섞여있다. 
![image](https://github.com/user-attachments/assets/f521e814-50b8-456e-bc11-a1e7f055b664)


번외로, 시작 시점은 다르나 도착 시점이 같은 것도 `sync`라고 보는 관점이 있다고 한다.
대표적으로, 자바 멀티쓰레드 환경에서 도착 시점을 맞추기 위해 먼저 도착 한 쓰레드가 다른 쓰레드를 기다리는 경우이다.(`CyclicBarrierThread`)
이는 매우 깊은 심해의 영역이니 이정도까지만 알고 넘어가자.

--- 

나머지도 알아가보자.
`sync + blocking`

```java
Scanner sc = new Scanner(System.in); // blocking, sync
int n = sc.nextInt();
int sum = n + n;
System.out.println(sum); // blocking, sync
```

`Scanner`로 입력을 받을 때 main thread에서 console 입력으로 제어권이 넘어갔기 때문에 `blocking` 상태가 되어 입력을 받을 때까지 실행하지 않고 대기한다.
입력을 받은 후에 블로킹 상태가 해제되어 다시 본 로직을 이어서 실행하고, `sum`을 출력할 때 또다시 `blocking` 상태가 되어 main thread는 실행하지 않고 대기한다.

동시에 제어권과 결과 값을 동시간대에 반환받으므로 `sync`를 만족한다.

또 다른 예시이다.

```java
ExecutorService executorService = Executors.newCachedThreadPool();  
Future<Integer> futureTask = executorService.submit(() -> factorial(number)); // non-blocking 

int result = futureTask.get(); // blocking, sync  

for (int i = 0; i < 10; i++) {
	System.out.println("is already done?")
}
assertThat(result).isEqualTo(120);  
  
executorService.shutdown();
```

아까 전 `Future` 코드에서 반복문 구문을 뺀 코드다.
이번에는 바로 `get()`으로 인해 `blocking`이 발생해서 실제로 main thread는 아무것도 안한 채 그저 기다리기만 한다.
그 후 `print`작업을 거치고 결과 값을 검증한다.

이처럼 우리에게 익숙한 프로그래밍 방식은 거의 `sync + blocking`으로 이루어져 있다고 생각하면 된다.
하지만 프레임워크의 내부는 멀티 쓰레드 등을 활용하여 `async`방식으로 구현된 경우가 많으므로, 이론을 확실히 알아가는 것이 도움된다.

> 우리가 앞으로 자바 및 스프링 계열로 커리어가 이어진다면, 95퍼 이상은 동기/blocking으로 작성 할 것이다.

---

`async + blocking`를 살펴보자.

이제 이들을 비교할 수 있다.
`async`는 결과 값 보다 제어권을 먼저 반환하는 것이며,
`blocking`은 결과 값이 올 때 까지 대기하는 것이다.

뭔가 논리 상 안맞는 부분이 있다. 어차피 `blocking`으로 인해 결과 값이 올 때 까지 아무것도 안하고 대기하는데, `async`는 그냥 제어권만 먼저 준다.
제어권을 반환 받아도 그냥 하염없기 기다리기만 할 뿐이다.

main에서 thread에게 요청을 보냈는데, main은 정작 관심 없는데 thread가 기다리라고 한 것과 같다.

> 주로 이벤트 기반 프로그래밍을 작성할 때 개발자의 실수로 생겨나는 경우가 많다고 한다. 그로 인한 성능 저하 이슈가 생긴다.

---

마지막으로 `async + non-blocking`을 살펴보자.

```javascript
fetch('url', option).then((response) => {
	return response.json();
}).then((data) => {
	something(data);
})
```
![image](https://github.com/user-attachments/assets/de15a5be-0d69-46df-966c-2b96797a4c2f)

대표적인 예시로 자바스크립트의 API 호출이 있다.

자바스크립트는 이벤트 기반 싱글 쓰레드로 동작한다. 근데 어떻게 `async`가 가능한걸까?
브라우저는 멀티쓰레드로 동작하기 때문이다. 그래서 이렇게 일거리를 던지고, `callback`을 사용하여 
각자 자신이 할 일을 하다가 완료되면 콜백함수가 호출되어 main에서 처리한다.

하지만 이 또한 완전한 `async`가 아니고, `sync`, `blocking`, `non-blocking`가 섞여 있다.

![image](https://github.com/user-attachments/assets/3b70180e-1161-4b91-940c-85d893ae0887)

자바에서도 `CompletableFuture`를 통해 callback함수를 지원한다.
그래도 `get()`을 호출할 때 만큼은 `sync + blocking`이 작동한다. 이것도 완전한 `async`는 아니란 의미.

---

지금까지 이렇게 `sync`/`async`와 `blocking`/`non-blocking`에 대해서 알아보았다.
정리하면,
- `block`은 제어권을 넘기는지 마는지
- `sync`은 제어권의 반환과 결과값 반환의 시간이 일치하는지
를 따지면 된다.

그리고 굳이 이분법적으로 나누는 것 보다, 제어권이 어디로 있는지 순서도를 보면서 파악하는 것이 좋다.

---

[[Java] Callable, Future 및 Executors, Executor, ExecutorService, ScheduledExecutorService에 대한 이해 및 사용법 - 망나니개발자](https://mangkyu.tistory.com/259)

[I/O Concepts in Linux - Jinho Ko](https://blog.jinhoko.com/i/o-concepts-in-linux/)

[[10분 테코톡] 🐰 멍토의 Blocking vs Non-Blocking, Sync vs Async - 우테코 멍토](https://www.youtube.com/watch?v=oEIoqGd-Sns)

[[10분 테코톡] 🎧 우의 Block vs Non-Block & Sync vs Async - 우테코 우](https://www.youtube.com/watch?v=IdpkfygWIMk&t=925s)
