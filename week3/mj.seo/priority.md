# Thread API - Priority

### Thread Priority

- 단일 `CPU`에서 여러 스레드를 실행하는 것을 **스케줄링**이라고 한다.
- `Java Runtime`은 고정 우선순위 **선점형 스케줄링**(fixed-priority pre-emptive scheduling)을 **지원**한다.
- `OS 스케줄러`가 **Java**에서 설정한 우선순위대로 `스레드`를 `실행`한다고 `보장`할 수 없다.

### 우선순위

- `Java`에서 스레드의 우선 순위는 **1**에서 **10**사이의 **정수**이다.
- `스레드`가 생성될 때 우선순위 값이 정해진다. 기본 값은 **5**이다.
- 두 스레드의 **우선순위**가 **같은** 경우에는 `RR(Round-Robin)` 스케줄링 방식에 의해 **선택**한다.
- `스케줄러`가 반드시 `우선순위`가 `높은` 스레드를 **실행한다고 보장** 할 수 없다.

### 우선 순위 Enum

- **최소 우선순위** - MIN_PRIORITY(1)
- **기본 우선순위** - NORM_PRIORITY(5)
- **최대 우선순위** - MAX_PRIORITY(10)

### 우선순위 변경 및 확인

- `void setPriority(int newPriority)`
    - 1-10 사이의 값으로 설정할 수 있다 → 범위를 넘어서는 값은 예외가 발생한다.
- `int getPriority()` - 스레드의 우선순위를 반환한다.

### 예제
```java
public class ThreadPriorityExample {

    public static void main(String[] args) throws InterruptedException {
        var maxThread = new CountingThread("우선 순위가 높은 스레드", Thread.MAX_PRIORITY);
        var normThread = new CountingThread("우선 순위가 일반 스레드", Thread.NORM_PRIORITY);
        var minThread = new CountingThread("우선 순위가 낮은 스레드", Thread.MIN_PRIORITY);

        maxThread.start();
        normThread.start();
        minThread.start();

        maxThread.join();
        normThread.join();
        minThread.join();

        System.out.println("작업 완료");
    }

    static class CountingThread extends Thread {
        private final String threadName;
        private int count = 0;

        public CountingThread(String threadName, int priority) {
            this.threadName = threadName;
            setPriority(priority);
        }

        @Override
        public void run() {
            while (count < 10000000) {
                count++;
            }

            System.out.println("Thread name = [" + threadName + "], count = [" + count + "]");
        }
    }
}

출력 -> 우선 순위가 높은 것부터 처리됨을 보장받을 수 없다.

Thread name = [우선 순위가 일반 스레드], count = [10000000]
Thread name = [우선 순위가 높은 스레드], count = [10000000]
Thread name = [우선 순위가 낮은 스레드], count = [10000000]
작업 완료
```
