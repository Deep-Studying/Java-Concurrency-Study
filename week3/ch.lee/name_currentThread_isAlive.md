# Thread API - name() / currentThread() / isAlive()

---
## name()

### Thread Name
- 어떤 스레드가 무슨 작업을 하고 있는지 정확하게 파악하기 위해 `스레드 이름을 정하는 것은 매우 도움`이 된다.
- 자바에서 스레드 생성시 `이름 자동 부여`(사용자 정의 안할 시)

   → 메인 스레드 이름 main, 이후 `Thread 0 … N 순차적 생성`(별도의 setName()으로 `n개를 만들면 0 + n번 부터 시작`)
- 자바에서 사용자가 스레드 이름을 정할 수 있으며 두 가지 방법 가능

1. 스레드 객체 생성 시 인자 전달
```java
Thread myThread = new Thread([ThreadGroup], [Runnable], "myThread");
```

2. SetName(String name)으로 설정
```java
myThread.setName("myThread");
```

### getName()으로 스레드 이름 참조
```java
myThread.getName();
```
---
## currentThread()
- `현재 실행 중인 스레드 개체`에 대한 참조를 반환

```java
Thread.currentThread().getName()

if(Thread.currentThread() == thread)
```

Thread내에서 작업하는 것은 `getName()으로 처리가 가능`합니다.

`익명 Thread나 Runnable` 구현 시에는 `Thread.currentThread()으로 처리`를 해야 한다.

---

## isAlive()

- 스레드 살아 있는 여부 확인(`true : 활성화, false : 비활성화`)