# Thread Local

### What is ThreadLocal?

- 각 `Thread`는 자신만이 **접근**해서 읽고 쓸 수 있는 **로컬 변수 저장소**인 `Thread Local`이 있다.
- `Thread`는 고유한 `ThreadLocal` 객체를 속성으로 가지고 있다.
- `Thread`는 `ThreadLocal`에 저장된 값을 **전역변수**처럼 **접근**해서 **사용**할 수 있다.
- 모든 `Thread`가 공통적으로 **처리**해야 하는 **기능**이나 `Thread` 마다 다른 값이 필요할 때 사용한다 (인증 주체 보관, 트랜잭션 전파 등)

### ThreadLocal API

- **`void set(T value)`**
    - 값을 **저장**
- **`T get()`**
    - 저장된 값을 **가져온다**
- **`void remove()`**
    - 저장된 값을 **삭제**
- **`withInitial(Supplier<? extends S> supplier)`**
    - **ThreadLocal**을 생성하면서 특정 값으로 **초기화**
- 사용 예시
    
    ```java
    private static final ThreadLocal<String> threadLocal = ThreadLocal.withInitial(() -> "defulat Name");
    
    threadLocal.set("Hello World");
    threadLocal.get();
    threadLocal.remove();
    ```
    

- 예제
    
    ```java
    private static final ThreadLocal<String> threadLocal = new ThreadLocal<>();
    
        public static void main(String[] args) {
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + " = [" + threadLocal.get() + "]");
                threadLocal.set("스레드 1의 값");
                System.out.println(Thread.currentThread().getName() + " = [" + threadLocal.get() + "]");
            }, "Thread-1").start();
    
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + " = [" + threadLocal.get() + "]");
                threadLocal.set("스레드 2의 값");
                System.out.println(Thread.currentThread().getName() + " = [" + threadLocal.get() + "]");
            }, "Thread-2").start();
        }
        
        // 각 스레드별로 다른 값이 나온다.
    ```
    

### Thread & ThreadLocal

- **`Thread`**는 **`ThreadLocal`**에 있는 **ThreadLocalMap** 객체를 자신의 **threadLocals** 속성에 저장한다.
- 최소 **Thread** 생성시 **threadLocals** 기본값은 **`null`** 이다.
- **Thread**가 **`ThreadLocal`**에 값을 저장할 때 **ThreadLocalMap**이 생성된다.
- **`ThreadLocalMap`**은 항상 새롭게 생성되어 **동시성 문제**가 발생하지 않는다.
    - **스레드 스택**에 저장되기 때문에 **스레드**간 **`데이터 공유`**가 안된다.

### 주의사항

- **`ThreadLocal`**에 저장된 값은 **스레드**마다 **독립적**이므로, 저장된 **`데이터`**를 삭제하지 않아도 메모리를 점유하는 것 외에 문제가 되지는 않는다.
- **`ThreadPool`** 사용시에는 **`ThreadLocal`** 에 저장된 값을 꼭 삭제해야한다.
- 이전의 스레드를 **재사용**하는 경우 이전에 사용했던 **`ThreadLocal`**의 데이터가 **참조**되어 **문제**가 될 수 있다.

### ThreadLocal 동작원리

- **`ThreadLocal`**은 **Thread**와 **`ThreadLocalMap`**을 연결하여 각 `스레드`별로 전용 저장소를 구현한다.
- **`Thread.currentThread()`**는 현재 실행중인 **스레드**의 객체를 **참조**하므로, 지금 실행 중인 스레드의 로컬 변수를 저장하거나 참조할 수 있다.
- **`Thread.currentThead()`** 는 **ThreadLocal**의 중요한 **데이터 식별 기준**이 된다.

### InheritableThreadLocal

- **`InheritableThreadLocal`**은 **부모 스레드**로부터 **자식 스레드**로 **`값을 전달`**할 수 있다.
- **`값의 상속`**
    - **부모 스레드**가 값을 **설정**하면 **부모 스레드**로부터 생성된 **자식 스레드**들은 **`값을 상속`**받는다.
- **`독립성`**
    - **`자식 스레드`**가 상속받은 값을 **변경**해도 부모 스레드의 **`값은 영향받지 않는다.`**
