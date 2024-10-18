### 개요

---

- join() 메서드는 한 쓰레드가 다른 쓰레드가 종료될 때 까지
  실행을 중지하고 대기 상태에 들어갔다가 쓰레드가 종료되면 실행대기 상태로 전환된다.
- 쓰레드의 순서를 제어하거나 다른 쓰레드의 작업을
  기다려야 하거나 순차적인 흐름을 구성하고자 할 때 사용한다.
- Object 클래스의 wait() 네이티브 메서드로 연결되며 시스템 콜을 통해
  커널모드로 수행한다. 내부적으로 wait() & notify() 흐름을 가지고 제어한다.

---

### API 및 예외 처리

---

- **void join () throws InterruptedException**
    - 쓰레드의 **작업이 종료될 떄 까지 대기 상태를 유지**한다.

    ---

- **void join(long millis) throws InterruptedException**
    - 지정한 **밀리초 시간 동안 쓰레드의 대기 상태를 유지**한다.
    - 밀리초에 대한 인수 값은 음수가 될 수 없으며 음수일 경우 IllegalArgumentException 발생

    ---

- **void join (long millis, int nanos) throws InterruptedException**
    - 지정한 밀리초에 나노초를 더한 시간 동안 쓰레드의 대기상태를 유지한다.
    - 나노초의 범위는 0에서 9999999이다.

    ---

- **InterruptedException**
    - 쓰레드가 **수면중에 인터럽트 될 경우 InterruptedException** 예외를 발생시킨다.
    - **다른 쓰레드는 잠자고 있는 쓰레드에게 인터럽트, 즉 중단  (멈춤) 신호를 보낼 수 있다.**
    - 예외가 발생하면 쓰레드는 수면 상태에서 **꺠어나고 실행대기 상태로 전환되어 실행상태를 기다린다.**

    ---

- 기본 코드 방식

```java
public static void main(String[] args) {
	Runnable r = new MyRunnable();
	Thread thread = new Thread(r);
	
	thread.start();
	
	try { // try-catch문으로 예외 처리 해 주어야 한다.
		thread.join(); // 메인 쓰레드는 thread의 작업이 종료될 떄 까지 일시정지한다.
		
	} catch (InterruptedException e) {
	  // 인터럽트 발생 시 예외 처리 필요
  }
}
```

- join() 작동 방식
    - wait() & notify()

        ```
          		Main Thread -> Running
        Scheduler ↗️
        			join() -> waint() -> WAITING -> Runnable
                  ↘️                                             
        			 Thread-1 -> Runnable -> Running -> Terminated -> notify()
        			 
        			 /** main thread는 대기상태로 인해 일시정지되고 Thread-1은 실행상태로 컨텍스트 스위칭
        			  thread-2가 모든 실행을 종료하게 되면, 대기 상태에 있는 메인쓰레드에게 통지를 날리고 대기상태 해지
        			 **/
        ```

    - interrupt() 발생

        ```
        					// main thread는 대기 상태로 인해 일시정지, thread-1은 실행 상태로 컨텍스트 스위칭 	      
        			 Main Thread -> Runnable
        			  ↗️                   
                                    wait() -> WAITING -> Runnable -> InterruptedException
        												join()
        				  interrupt()
        Scheduler →	Thread-1 -> Runnable
        			  ↘️
        			    Thread-2 -> Runnable
        			 
        // thread-2가 main 에게 인터럽트를 걸어 예외를 발생시키고 메인 쓰레드가 대기에서 해제되고 실행 대기상태로 전환
        ```

- join() 작동 방식 정리

    ```
    1. join()을 실행하면 OS 스케줄러는 join()을 호출한 쓰레드를 대기상태로 전환, 호출 대상 쓰레드에게 CPU를 사용하도록 한다.
    2. 호출 대상 쓰레드의 작업이 종료되면, join()을 호출한 쓰레드는 실행 대기 상태로 전환되고 CPU가 실행을 재개할 때 까지 기다린다.
    3. join()을 호출한 쓰레드가 실행 대기에서 실행 상태가 되면 그 쓰레드는 남은 지점부터 실행을 다시 시작한다.
    4. 호출 대상 쓰레드가 여러개일 경우 각 쓰레드의 작업이 종료될 때 까지 join()을 호출한 쓰레드는 대기와 실행을 재개하는 흐름을 반복한다.
    5. join()을 호출한 쓰레드가 인터럽트 되면 해당 쓰레드는 대기에서 해제되고 실행상태로 전환되어 예외를 처리하게 된다.
    ```