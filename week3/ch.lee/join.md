# Thread API - join()

- 한 스레드가 다른 스레드가 종료될 때까지 실행을 중지하고 대기상태 → 대상 스레드가 종료되면 실행 대기 상태로 전환

- 순서를 제어하거나 `다른 스레드 작업을 기다려야 하거나 순차적인 흐름`을 구성할 때 사용

- Object 클래스의 wait() 네이티브 메서드로 연결되며, 시스템 콜을 통해 커널모드로 수행, 내부적으로 `wait() & notify()` 흐름을 가지고 제어한다.

---
## join() In JAVA


```java
// 스레드의 작업 종료까지 대기 상태 유지(현재 스레드가 대상 스레드 종료까지 대기)
void join() throws InterruptedException
```

```java
//지정한 밀리초 시간 동안 스레드 대기 상태 유지
//밀리초에 대해서 음수가 될 수 없으므로 음수이면 IllegalArgumentException 발생
void join(long millis) throws InterruptedException
```
```java
//밀리 초 + 나노 초 더한 시간 동안 스레드 대기 상태 유지
//나노초 범위(0 ~ 999999)
void join(long millis, int nanos) InterruptedException
```
---
## join() 작동 방식
- join()실행 시 OS 스케줄러는 join()을 호출한 스레드는 대기 상태로 전환 → 호출 스레드는 CPU 사용
- 호출 대상 스레드 작업 종료 → join()을 호출한 스레드는 실행 대기 상태로 전환
- join()을 호출한 스레드가 실행 대기에서 실행 상태가 되면 남은 지점부터 실행
- join 호출 스레드가 여러 개인 경우 → 각 스레드의 작업이 종료될 때까지 join()을 호출한 스레드는 `대기와 실행을 재개하는 흐름을 반복`
- join()을 호출한 스레드가 `인터럽트 되면 해당 스레드는 대기에서 해제되고 실행상태로 전환되어 예외를 처리`
---
## InterruptedException
- 스레드가 인터럽트 되면 `InterruptedException` 발생
- 다른 스레드는 join()을 수행중인 `스레드에게 인터럽트(중단) 신호 가능`
- InterruptedException 발생 → `대기 상태에서 실행 대기 상태로 전환`

```java
public static void main(String[] args) {
    Runnable r = new MyRunnable();
    Thread thread = new Thread(r);
    thread.start();
    try {
        thread.join();
    }catch (InterruptedException e) {

    }
}
```