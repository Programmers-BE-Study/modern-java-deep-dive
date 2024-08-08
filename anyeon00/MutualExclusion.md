## 프로세스 동기화

<aside>
💡 **비동기 병행 프로세스**

- 비동기적 : 각 프로세스들이 서로 진행 상태를 모름
- 병행성 : 다수의 프로세스가 동시 존재
</aside>

여러 프로세스가 독립적으로 존재하며 원활하게 동작하도록 하는 것

**문제 발생 :** 공유데이터에 동시 접근 시

ex)

1. 고급 언어에서의 코드

```c
x = x + 1;
```

1. Assembly 코드

```nasm
LOAD 15, x        : 메모리의 x값을, 15번 register에 load
ADD 15, = F'1'    : 15번 register의 값에 1 더하기
STO 15, x         : 15번 register의 값을, 메모리의 x에 저장
```

---

### 예시 상황

```c
int count = 100;
main(){
	//Thread1이 run() 실행;
	//Thread2이 run() 실행;
}
int run(){
	for(int i = 0; i < 10000; i++){
		//critical section
		count = count + 1;
		//critical section
	}
}
```

### 임계구역에 진입 후 , TimeQuantum이 끝난 경우

⇒ 현재 count 값 = 100

Thread1의 진행상태

1.

```nasm
LOAD 15, count    : reg = 100 
ADD 15, = F'1'    : reg = 101
```

3.

```nasm
STO 15, count     : count = 101
```

Thread2의 진행상태

2.

```nasm
LOAD 15, count   : reg = 100 
ADD 15, = F'1'   : reg = 101
STO 15, count    : count = 101
```

⇒ 결과 : count 값 = 101

---

### 의도한 실행 결과

⇒ 현재 count 값 = 100

Thread1의 진행상태

1.

```nasm
LOAD 15, count   : reg = 100 
ADD 15, = F'1'   : reg = 101
STO 15, count    : count = 101
```

Thread2의 진행상태

2.

```nasm
LOAD 15, count   : reg = 101 
ADD 15, = F'1'   : reg = 102
STO 15, count    : count = 102
```

⇒ 결과 : count 값 : 102

---

### 임계구역에 대한 상호배제 필요

```c
int count = 100;
main(){
	//Thread1이 run() 실행;
	//Thread2이 run() 실행;
}
int run(){
	for(int i = 0; i < 9999999; i++){
		//critical section
		//-- 하나의 스레드만 진입 가능 --//
		count = count + 1;
		//critical section
		//-- 임계구역을 빠져나온 후, 다른 스레드가 진입 가능 --//
	}
}
```

## 상호배제 기법

1. Primitive 코드로 구현

1 -2. Dekker알고리즘, Peterson알고리즘

ex) Primitive 구현 예시

```c
int count = 100;
int turn = 0;    //상호배제를 위한 변수 추가
main(){
	//Thread1이 run() 실행;
	//Thread2이 run() 실행;
}
int run(){
	for(int i = 0; i < 9999999; i++){
		count = count + 1;
	}
}
```

Thread1

```c
int run(){
	for(int i = 0; i < 9999999; i++){
		//turn == 1일때, 진입가능
		while(turn == 0){
		}
		count = count + 1; //CS구역
		//실행 후, turn 변경
		turn = 0; 
	}
}	
```

Thread2

```c
int run(){
	for(int i = 0; i < 9999999; i++){
	  //turn == 0일때, 진입가능
		while(turn == 1){
		}
		count = count + 1; //CS구역
		//실행 후, turn 변경
		turn = 1;
	}
}	
```

2. TS() 명령어 (Test and Set)

```c
while(Test-and-Set(&lock));	// true가 return되면 while문에 갇힘, false가 return되면 진입
count = count + 1; //--Critical Section--
lock = 0;		// 누가 들어가있으면 lock == 1
```

- block시킴 → cpu사용 x

3. 세마포어 기법

```c
P(active);  //들어갈 때 사용가능 자원 감소
count = count + 1; //--Critical Section--
V(active);  //나올 때 사용가능 자원 증가
```

- suspended시킴 → 메모리사용 x

4. JAVA의 Modifier : synchronized
- 해당 메서드에 대해 상호배제 적용

```java
public synchronized void caculate(){
	count++;
}
```

### 찾아볼 거리

- Reader-Writer 문제
- 생산자-소비자 문제