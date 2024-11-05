# Thread 중지 - flag variable vs interrupt()
### Thread 중지

- **`자바`**에서는 무한 반복(지속 실행) 중에 있는 **`Thread`**를 **중지**하거나 **종료**할 수 있는 **API**를 사용할 수 없다(**`suspend()`**, **`stop()`**)
- **`Thread`**를 **종료**하는 방법은 **`플래그 변수`**를 사용하거나 **`interrupt()`**를 활용해서 **구현**할 수 있다

### Flag Variable

- **`Flag 변수`**의 값이 어떤 조건에 만족할 경우 **`Thread`**의 **실행**을 **중지**하는 **방식**이다.
- **`Flag 변수`**는 동시성 문제로 가능한 **`atomic 변수`**나 **`volatile 키워드`**를 사용하는 것이 좋다.

### interrupt() & isInterrupted()

- 실행 중인 **스레드**에 **`interrupt()`** 하게 되면 **종료기능**을 **구현**할 수 있다.
- **`interrupt()`** 한다고 해서 **스레드**가 처리하던 **작업**이 **중지**되는 것은 아니다.
- **`Thread.interrupted()`** 는 인터럽트 상태를 해제한다.
- **`Thread.currentThread().isInterrupted()`** 는 인터럽트 상태를 유지한다.

```java
private static boolean running = true;

    public static void main(String[] args) {

        new Thread(() -> {
            int count = 0;
            while (running) {
                count++;
            }
            System.out.println("Thread 1 종료, count = [" + count + "]");
        }).start();

        new Thread(() -> {
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
            System.out.println("Thread 2 종료");
            running = false; // 변수의 값을 바꿔도 무한루프가 걸린다.
        }).start();

    }
    
 
 @SuppressWarnings("BusyWait")
    public static void main(String[] args) {

        new Thread(() -> {
            int count = 0;
            while (running) {
                try {
                    /*
                     * Thread 간 컨텍스트가 다르기 때문에 Context-Switching을 하여 제대로 된 running 값을 바라볼 수 있다. 
                     * -> volatile 키워드를 쓰거나, Atomic prefix가 붙은 변수를 써도 해결이 가능하다.
                     */ 
                    Thread.sleep(1);                   
                } catch (InterruptedException e) { 
                  throw new RuntimeException(e);
                }
                count++;
            }
            System.out.println("Thread 1 종료, count = [" + count + "]");
        }).start();

        new Thread(() -> {
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
            System.out.println("Thread 2 종료");
            running = false; // 변수의 값을 바꾸면 종료된다.
        }).start();

    }
```

### 주의할 점

- **`join`**이나 **`sleep`**과 같은 **`API`**를 사용하지 않고 직접 **InterruptedException**을 **발생**시키면 **`isInterrupted`** 변수가 **`false`**로 초기화 되지 않는다.

```java
if (Thread.currentThread().isInterrupted()) throw new InterruptedException("thread is Interrupted");
```
