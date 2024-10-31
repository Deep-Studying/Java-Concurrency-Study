### ThreadLocal

개요

---

- 오직 자신만이 접근해서 읽고 쓸 수 있는 로컬 변수 저장소를 ThreadLocal이라 한다.
- 각 쓰레드는 고유한 ThreadLocal 객체를 속성으로 가지고 있고, 쓰레드 간 격리되어 있다.
    - 격리 보장을 어떻게 하는지?
- 쓰레드는 ThreadLocal에 저장된 값을 특정 위치나 시점에 상관없이 전역변수처럼 사용가능하다.
- 모든 쓰레드가 공통적으로 처리해야 하는 기능이나 객체를 제어해야 하는 상황에서 쓰레드마다 다른 값을 적용해야 하는 경우 사용한다. (인증 주체 보관, 트랜잭션 전파, 로그 추적기 등)
- Spring의 `Transactional` 어노테이션도 내부적으로 쓰레드 로컬을 사용하고 있다.

---

### ThreadLocal API

---

- **void set(T value)**
    - 쓰레드 로컬에 `값을 저장한다.`
- **T get()**
    - 쓰레드 로컬에 `저장된 값을 가져온다.`
- **void remove()**
    - 쓰레드 로컬에 `저장된 값을 삭제한다.`
- **withInitial(Supplier<? extends S> supplier)**
    - 쓰레드 로컬을 생성하면서 `특정 값으로 초기화한다.`

```
private static final ThreadLocal<String> threadLocal =
			ThreadLocal.withInitial(() -> "defaultName"); // 생성 시 디폴트 값 초기화

				threadLocal.set("Hello World"); // 저장
				threadLocal.get(); // 조회
				threadLocal.remove(); // 제거
```

---

### Thread & ThreadLocal

---

- 쓰레드는 ThreadLocal에 있는 ThreadLocalMap 객체를 자신의 threadLocals 속성에 저장한다.
- 쓰레드 생성 시 threadLocals 기본값은 Null, ThreadLocal에 값을 저장할 때 ThreadLocalMap이 생성되고, threadLocal과 연결된다.
- 쓰레드가 전역적으로 값을 참조할 수 있는 원리는 쓰레드가 ThreadLocal의 ThreadLocalMap에 접근해서 여기에 저장된 값을 바로 꺼내어 쓸 수 있기 때문이다.

```
Thread                                    ThreadLocal    ThreadLocalMap Entty

ThreadLocal.ThreadLocalMap threadLocals - ThreadLocalMap - Entry -      value
```

- ThreadLocalMap은 항상 새롭게 생성되어 쓰레드 스택에 저장되기 떄문에 쓰레드 간 데이터 공유가 될 수 없다. 따라서 동시성 문제가 발생하지 않는다.
    - 왜일까?

### 주의사항

---

- ThreadLocal에 저장된 값은 쓰레드마다 독립적으로 저장되기에 저장된 데이터를 삭제하지 않아도 메모리를 점유하는 것 외에 문제가 되지 않는다.
- 그러나 쓰레드 풀을 사용해서 쓰레드를 운용한다면, 반드시 ThreadLocal에 저장된 값을 삭제해 주어야 한다.
- 쓰레드풀은 쓰레드를 재사용하기 떄문에 현재 쓰레드가 이전의 쓰레드를 재사용한 것이라면 이전의 스레드에서 삭제하지 않았던 데이터를 참조할 수 있기 때문에 문제가 될 수 있다.

---

### ThreadLocal 작동원리

---

- ThreadLocal은 Thread와 ThreadLocalMap을 연결하여 쓰레드 전용 저장소를 구현하는데, 이것이 가능한 이유는 Thread.currentThread()를 참조할 수 있기 때문이다.
- Thread.currentThread()는 현재 실행중인 쓰레드 객체를 참조하는 것으로서 CPU는 오직 하나의 쓰레드만 할당받아 처리하기 때문에 ThreadLocal에서 Thread.currentThread()를 참조하면서 지금 실행중인 쓰레드의 로컬 변수를 저장하거나 참조할 수 있게 된다.
- ThreadLocal에서 현재 쓰레드를 참조할 수 있는 방법이 없다면 값을 저장하거나 요청하는 쓰레드를 식별할 수 없기에 Thread.currentThread()는 ThreadLocal의 중요한 데이터 식별 기준이된다.

---

데이터 저장

```
Thread set(data) - ThreadLocal getMap() - t.threadLocals == null ? (N) - ThreadLocalMap
														    ㄴ
				(Y) new ThreadLocalMap()  (data) ->	  Entry
```

데이터 조회

```
Thread get() - ThreadLocal getMap() - t.treadLocals == null ? (N) - ThreadLocalMap getEntry() - Entry
                                                       (Y) setInitialValue(data)																																																value
```

---

### InheritableThreadLocal

---

- InheritableThreadLocal은 ThreadLocal의 확장 버전으로 부모 쓰레드로부터 자식 쓰레드로 값을 전달하고 싶을 경우 InheritableThreadLocal을 사용할 수 있다.
- 값의 상속:
    - 부모 쓰레드가 IneritableThreadLocal 변수에 값을 설정하면, 해당 부모 쓰레드로부터 생성된 자식 쓰레드들은 부모의 값을 상속받게 되낟.
- 독립성:
    - 자식 쓰레드가 상속받은 값을 변경하더라도 부모 쓰레드의 값에 영향을 주지 않는다.

    ```java
    public static InheritableThreadLocal<String> inheritableThreadLocal = new InheritableThreadLocal<>();
    	public static void main(String[] args) {
    	inheritableThreadLocal.set("부모 쓰레드 값");
    	Thread childThread = new thread(() -> {
    
    	// 부모 쓰레드로부터 값 상속
    	System.out.println("자식 쓰레드에서 상속받은 값: " + inheritableThreadLocal.get()); // 부모 쓰레드의 값
    	
    	// 자식 쓰레드에서 값을 변경
    	inheritableThreadLocal.set("자식 쓰레드의 새로운 값");
    	System.out.printn("자식 쓰레드에서 설정한 후의 값: " + inheritableThreadLocal.get()); // 자식 쓰레드의 새로운 값
    });
    
    childThread.start();
    //부모 쓰레드 값 확인 	
    System.out.println("부모 쓰레드의 값: " + inheritableThreadLocal.get()); // 부모 쓰레드 값