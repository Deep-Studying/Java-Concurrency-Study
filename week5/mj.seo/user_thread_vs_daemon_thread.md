# User Thread vs Daemon Thread
### Thread의 종류

- 자바의 **`Thread`** 유형은 **`User Thread`**와 **`Daemon Thread`**로 나눌 수 있다.
- **`사용자 스레드`**는 사용자 스레드를 낳고 **`데몬 스레드`**는 데몬 스레드를 낳는다.
    - **자식** 스레드는 **부모** 스레드의 **`상태`**를 **`상속`**받는다.
- 자바 애플리케이션이 시작되면 `JVM`은 사용자 스레드인 **메인 스레드**와 나머지 **데몬 스레드**를 **생성**하고 **시작**한다.

### Main Thread

- 메인 스레드는 애플리케이션이 실행될 때 생성되어 실행된다.
- 메인 스레드는 애플리케이션의 시작이자 끝 역할을 한다.
- 메인 스레드에서 여러 하위 스레드를 추가로 시작할 수 있다.
- 메인 스레드가 사용자 스레드이므로 하위 스레드는 모두 유저 스레드가 된다.

### User Thread

- **`사용자 스레드`**는 메인 스레드에서 **직접 생성**한 스레드다.
- **`사용자 스레드`**는 각각 독립적인 **생명주기**를 가지고 **실행**되며, **`메인 스레드`**를 포함한 모든 사용자 스레드가 종료되면 **애플리케이션**이 **종료**된다.
- 사용자 스레드는 **`foreground`**에서 실행되는 **높은 우선순위**를 가지고, **`JVM`**은 사용자 스레드가 스스로 종료될때까지 **강제 종료**하지 않고 기다린다.
- **`ThreadPoolExecutor`**는 **사용자 스레드**를 생성한다.

### Daemon Thread

- **`데몬 스레드`**는 **JVM**에서 **생성**하거나 직접 **`데몬 스레드`**로 **생성**할 수 있다.
- 모든 **`사용자 스레드`**가 작업을 완료하면 **`데몬 스레드`**의 **실행 여부**에 관계없이 **JVM**이 데몬 스레드를 **강제로 종료**한다.
- **`데몬 스레드`**의 생명주기는 사용자 스레드에 따라 다르고, **`낮은 우선순위`**를 가지고 **`background`**에서 실행된다.
- **`데몬 스레드`**는 사용자 스레드를 **보조** & **지원**하는 **역할**을 한다.
- **`ForkJoinPool`**은 데몬 스레드를 **생성**한다.
- **`데몬 스레드`** 생성 예시
    
    ```java
    // void setDaemon(boolean on) 사용
    thread.setDaemon(true);
    
    // boolean isDaemon() -> 이 스레드가 데몬 스레드인지 확인
    boolean isDaemon = thread.isDaemon();
    ```
    
- **`Thread`** 실행 중에 **`setDaemon()`**을 **호출**하면 **`IllegalThreadStateException`**이 **발생**한다.

```java
// UserThread의 자식도 UserThread 이므로 둘 다 false가 출력된다.
Thread userThread = new Thread(() -> {
            new Thread(() -> {
                System.out.println("사용자 스레드의 자식 스레드의 데몬 상태  = ["  + Thread.currentThread().isDaemon() + "]");
            }).start();
            System.out.println("사용자 스레드의 데몬 상태 = [" + Thread.currentThread().isDaemon() + "]");
        });

// DaemonThread의 자식도 DaemonThread이므로 둘 다 true가 출력된다.
Thread daemonThread = new Thread(() -> {
            new Thread(() -> {
                System.out.println("데몬 스레드의 자식 스레드의 데몬 상태  = ["  + Thread.currentThread().isDaemon() + "]");
            }).start();
            System.out.println("데몬 스레드의 데몬 상태 = [" + Thread.currentThread().isDaemon() + "]");
        });
```
