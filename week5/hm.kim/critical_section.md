### 임계영역(CriticalSection)

개요

---

- `entry`, `critica`, `exit`, `remainder` 등의 섹션으로 구성된다.
- 둘 이상의 쓰레드가 동시에 접근해서는 안되는 공유자원(자료구조 또는 장치)에 접근하는 코드영역

---

### 영역구분

| 입장영역(entry section) | 임계영역에 진입하기 위해 허가 요청하는 영역 |
| --- | --- |
| 임계영역(critical section) | 하나의 스레드만 접근할 수 있는 영역 |
| 퇴장영역(exit section) | 임계영역에서 빠져나올 떄 신호를 알리는 영역 |
| 나머지영역(remainder section) | 위의 3가지 영역을 제외한 영역 |

```java
public class SharedObject {
    ReentrantLock lock = new ReentrantLock();
    int counter = 0;

    public void perform() {
        lock.lock(); // entry section, 락을 획득해야 임계영역에 진입할 수 있다.
        try {
            count++; // critical section, 오직 하나의 쓰레드만 이 영역에서 실행할 수 있다.
        } finally {
            lock.unlock(); // exit section, 락을 해제해서 다른 쓰레드에게 락을 획득할 수 있도록 한다.
            log.info("finished"); // remainder section
        }
    }
}
```

### Critical Section Problem

---

- 한 쓰레드가 임계영역 실행하고 있을 때, 다른 쓰레드가 같은 임계영역을 사용함으로 발생하는 문제
- 이 문제의 해결책에는 3가지 충족 요건이 요구된다.
    - **Mutual Exclusion (상호배제)**
        - 어떤 쓰레드가 임계영역을 `실행 중`이면 다른 쓰레드는 `동일한 임계영역`을 실행할 수 없다.
    - **Progress (진행)**
        - 임계 구역에서 실행중인 쓰레드가 없고, 임계 구역에 진입하려는 쓰레드가 있을 때
          `어떤 쓰레드가 들어갈 것인지` 적절히 선택해줘야 하며 `결정을 무한정 미뤄서는 안된다`.
    - **Bounded Waiting (한정 대기)**
        - 다른 쓰레드가 임계영역에 들어가도록 요청한 후 해당 요청이 수락되기
          전에 기존 쓰레드가 임계영역에서 실행할 수 있는 `횟수`에 `제한`이 있어야 한다.
        - **`Starvation(기아상태)`** 가 발생하지 않도록 한다. → 기아상태가 무엇인가

---

### 동기화 도구들

---

- **뮤텍스**, **세마포어**, **모니터**, **CAS**와 같은 동기화 도구를 통해 `Critical Section Problem`이
  발생하지 않도록 할 수 있으며 자바에서는 `synchronized`를 포함한 여러 동기화 도구들을 제공

---

### 경합상태, 경쟁조건, 경쟁상태 (Race Condition)

---

- 여러 쓰레드가 **동시**에 `공유 자원에 엑세스하고 조작할 때`쓰레드  
  간 **엑세스하는 순서**나 **시점**에 따라  `실행결과`가 달라질 수있는데, 이를 `Race Condition` 라 한다.
- Race Condition은 Critical section Problem이 해결되지 않은 상태에서 여러 쓰레드가
  동시에 임계영역에 접근해서 공유 데이터를 조작함으로써 발생하는 상태라 할 수 있다.

---

### 핵심

---

- **데이터**의 `보호`가 **필요**한 영역 → `크리티컬 섹션`
- 동기화 영역이 노출되면 온전한 데이터로 계산되지 않는다.

---