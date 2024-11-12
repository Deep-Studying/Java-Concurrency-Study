# Critical Section

### Critical Section

- 둘 이상의 **`스레드`**가 **공유자원**에 접근하는 **코드 영역**을 말한다.
- entry section, critical section, exit section, remainder section 으로 구성된다.
    - **`entry section`** - **critical section**에 진입하기 위해 **허가**를 **요청**하는 영역
    - **`critical section`** - **하나의 스레드**만 접근할 수 있는 영역
    - **`exit section`** - **critical section**에서 나올때 **신호**를 알리는 영역
    - **`remainder section`** - entry, critical, exit section을 제외한 나머지 영역
    

```java
public void perform() {
   lock.lock();
   
   try {
     count++;
   } finally {
     lock.unlock();
     log.info("finished");
   }
}
```

### Critical Section Problem

- 한 **스레드**가 **`critical section`**을 실행하고 있을 때 다른 스레드가 같은 **`critical section`**을 사용함으로 발생하는 **문제**다.
- **`문제 해결 방법`**
    - **`Mutual Exclusion`** (상호 배제) → 동시에 두 개 이상의 스레드가 **critical section**을 동시에 실행할 수 없다.
    - **`Progress`** (진행) → **critical section**에 실행 중인 스레드가 없으면 어떤 스레드가 들어갈지 **적절히 선택**해줘야 하며, **결정**이 무한정 미뤄지면 안된다.
    - **`Bounded Waiting`** (한정 대기) → 다른 스레드가 **임계 영역**에 들어가려고 **허가**를 **요청**했을때 기존 스레드의 **수행 횟수**를 **제한**함으로서 **기아 상태**가 발생하지 않도록한다.
- **`예시`**
    
    ```java
    class SharedResource {
        private int counter = 0;
        
        public void increment() {
            for (int i=0; i<100000; i++) {
                synchronized (this) { // Entry Section
                    
                    // Critical Section
                    counter++;
                    System.out.println(Thread.currentThread().getName() + " = [" + counter + "]");
                } // Exit Section
                
                // Remainder Section
                doOtherWork();
            }
        }
    }
    ```
    
### Race Condition

- 여러 **`스레드`**가 동시에 공유 자원에 **`액세스`**하고 **`조작`**할 때 **`스레드`**의 접근 순서에 따라 **`최종 결과`**가 달라질 수 있는 문제를 **`경쟁 상태`**라고 한다.
- **`Critical Section Problem`** 이 해결되지 않은 상태에서 **`여러 스레드`**가 동시에 자원에 **`접근`**하면 **발생**하는 **상태**다.
