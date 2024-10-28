# Thread API - join()

### what is join()?

- `join()` 메서드는 한 `스레드`가 다른 `스레드`가 종료될 때까지 **실행**을 **중지**하고 **대기 상태**로 들어간다.
- `다른 스레드`가 종료되면 **실행대기 상태**로 **전환**된다.
- `순차적인 흐름`을 구성하고자 할 때 **사용**할 수 있다.
- **Object** 클래스의 **wait()** 네이티브 메서드로 연결되어 `커널 모드`로 수행된다.
- 내부적으로 **wait()** & **notify()** 흐름을 가지고 **제어**한다.

### API 및 예외처리

- `void join() throws InterruptedException`
    - 스레드의 **작업**이 **종료** 될 때까지 **대기 상태**를 **유지**한다.

- `void join(long millis) throws InterruptedException`
    - 지정한 **밀리초** 시간 동안 스레드의 **대기 상태**를 **유지**한다.
    - 스레드의 **작업**이 **종료**되지 않아도, 시간이 지나면 깨어난다.
    
- `void join(long millis, int nanos) throws InterruptedException`
    - 지정한 **밀리초**에 **나노초**를 더한 시간 동안 `스레드`의 **대기 상태**를 **유지**한다.
    - 스레드의 `작업`이 **종료**되지 않아도, 시간이 지나면 깨어난다.

- `InterruptedException`
    - 다른 스레드는 **join()**을 수행 중인 스레드에게 **Interrupt**를 걸 수 있다.

```jsx
// 예시
public static void main(String[] args) {
	Runnable r = new MyRunnable();
	Thread thread = new Thread(r);
	
	thread.start();
	
	try {
	  Thread.join(); // 메인 스레드가 대기한다.
	} catch (InterruptedException e) {
	  throw new RuntimeExcepiton(e);
	}
}
```

### join()의 동작 방식

- `wait() & notify()`
    - **join()**을 호출하면 `thread-1`이 **wait()**이 호출되고 일시정지된다.
    - `thread-2`가 모든 작업을 끝내면 대기 상태에 있는 `thread-1`에게 **notify()**를 통해 `대기상태`를 해제하도록 한다.
    
    ```java
    public class BasicJoinExample {
    
        public static void main(String[] args) {
    
            Thread thread = new Thread(() -> {
                try {
                    System.out.println("스레드가 3초 동안 작동합니다. Thread Name = " + Thread.currentThread().getName());
                    Thread.sleep(3000);
                    System.out.println("스레드 작동 완료. Thread Name = " + Thread.currentThread().getName());
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
    
            thread.start();
    
            System.out.println("메인 스레드가 다른 스레드의 완료를 기다립니다.");
    
            try {
                thread.join();
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
    
            System.out.println("메인 스레드 종료. Thread Name = " + Thread.currentThread().getName());
        }
    }
    
    ```
    

- `interrupt() 발생`
    - **join()**을 호출하면 `thread-1`이 **wait()**이 호출되고 일시정지된다.
    - `thread-2`가 `thread-1`에게 **interrupt**를 걸면, `thread-2`의 작업이 처리되지 않았어도 `thread-1`은 즉시 깨어나서 **실행 대기 상태**로 전환된다.
    - `thread-1`이 실행 상태가 되면 **InterruptedException**을 처리한다.
    
    ```java
    public class InterruptedJoinExample {
    
        public static void main(String[] args) {
    
            Thread longRunningThread = new Thread(() -> {
                try {
                    for (int i=0; i<10; i++) {
                        System.out.println("긴 작업 스레드가 계속 실행 중...");
                        Thread.sleep(1000);
                    }
                } catch (InterruptedException e) {
                    System.out.println("긴 작업 스레드가 인터럽트 되었습니다.");
                }
            });
    
            longRunningThread.start();
    
            new Thread(() -> {
                try {
                    System.out.println("인터럽트 스레드가 2초 후에 긴 작업 스레드를 인터럽트 합니다.");
                    Thread.sleep(2000);
                    longRunningThread.interrupt();
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }).start();
        }
    }
    ```
    

### join() TMI

- `join()`을 호출하면 `OS 스케줄러`는 **join()**을 호출한 스레드를 대기 상태로 전환한다.
- 호출 대상 `스레드`가 여러 개일 경우 각 **스레드**의 작업이 **종료**될 때 까지 `join()`을 호출한 스레드는 대기와 실행을 반복한다.
- `join()`을 호출한 스레드가 **interrupt** 되면 대기에서 해제되고 `실행 대기 상태`로 전환되고 실행 상태가 되었을때 **예외**를 **처리**한다.
