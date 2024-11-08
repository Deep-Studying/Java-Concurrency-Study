# Thread API - currentThread(), isAlive(), Thread Name

### Thread Name

- `멀티 스레드` 환경에서 `스레드`에 사용자 이름을 지정하면 **Debugging** 할 때 쉽게 찾을 수 있다.
- `자바`에서 **스레드**가 생성되면 이름이 자동으로 주어진다.
    - 가장 먼저 생성되는 스레드는 `main` 이다.
    - **main 스레드** 이외의 스레드는 `Thread-0`, `Thread-1`.. 과 같이 숫자가 증가하면서 만들어진다.

- `스레드 이름` 설정 방법
    - `Thread` 객체 생성시 **생성자**로 전달
        
        ```java
        Thread myThread = new Thread([ThreadGroup], [Runnable], "myThread");
        ```
        
    
    - `setName()` 으로 설정
        
        ```java
        Thread myThread = new Thread(() -> { System.out.println("Hi Thread"); });
        myThead.setName("myThread");
        ```
        

### currentThread()

- 현재 실행중인 `스레드 객체`에 대한 **참조**를 **반환**한다.
- 사용 예시
    
    ```java
    Thread.currentThread.getName(), if(Thread.currentThread() == thread)
    ```
    

### isAlive()

- `스레드`가 살아있는지 **여부**를 **반환**한다.
- `스레드`의 **start()** 메서드가 호출되고 **스레드**가 종료되지 않으면 **true**를 반환한다.
- 사용예시
    
    ```java
    public class ThreadAliveExample {
    
        public static void main(String[] args) {
    
            Thread task1 = new Thread(() -> {
                for (int i=0; i<5; i++) {
                    System.out.println("작업 스레드 1 실행 중...");
    
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            });
    
            Thread task2 = new Thread(() -> {
                for (int i=0; i<5; i++) {
                    System.out.println("작업 스레드 2 실행 중...");
    
                    try {
                        Thread.sleep(150);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            });
    
            task1.start();
            task2.start();
    
            while (task1.isAlive() || task2.isAlive()) {
                System.out.println("작업 스레드 1 상태 = [" + task1.isAlive() + "]");
                System.out.println("작업 스레드 1 상태 = [" + task2.isAlive() + "]");
    
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
    
            System.out.println("모든 스레드 작업이 완료되었습니다.");
    
        }
    }
    ```
