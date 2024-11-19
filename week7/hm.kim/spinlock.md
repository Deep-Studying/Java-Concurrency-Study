### 스핀락

개요

---

쓰레드가 임계영역을 사용할 수 있을 때 까지 반복하여 검사하는 동기화 메커니즘

---

### 작동방식

---

```
voliatile int lock = 0;
void critical() {
  while(test_and_set(&lock) == 1);
  // critical section
  lock = 0;
}

int test_and_set(int *lock) {
	int oldValue;
	oldValue = *lock;
	*lock = 1
	return oldValue;
}
```

1. 쓰레드가 공유자원에 접근하려 할 떄 스핀락 시도
2. test_and_set() 함수가 이전 락 값인 0을 반환하면 while loop 빠져나온다.
3. 쓰레드는 스핀락을 얻고 해당 자원을 사용한다.
4. 그러나 이전 락 값이 1이면 이미 다른 쓰레드에 의해 잠긴것을 의미하며,
   스핀락을 얻을 떄 까지 계속해서 반복적으로 검사를 수행한다.
5. 쓰레드가 자원 사용이 끝나면 lock을 0으로 변경해서 스핀락을 해제한다.

---

### Busy Waiting - 바쁜 대기

---

- 쓰레드가 어떤 조건이 만족될 때까지 계속 반복적으로 검사하는 것
- 쓰레드가 특정 조건을 기다리는 동안 작업을 수행하지 않고 무한루프
  반복을 돌며 CPU 자원을 계속해서 사용하는 것을 의미한다.
- 스핀락은 이러한 busy waiting을 사용하는 동기화 기법 중 하나이다.

```java
/** 예시, compareAndSet === CAS **/

public class SpinLockExample {
    private AtomicBoolean lock = new AtomicBoolean(false);

    public void lock() {
        while (!lock.compareAndSet(false, true)) ;
    }

    public void unlock() {
        lock.set(false);
    }
}
```

---

### 스핀락의 장/단점

---

- **장점**
    - 컨텍스트 스위칭 비용 감소
        - 뮤텍스나 세마포어 같은 블로킹 기반 동기화 기법이 아닌
          반복적으로 검사한다. 따라서 스핀락을 빠른 공유 자원 접근을 가능하게 한다.
    - 대기시간 감소
        - 스핀락은 블로킹 대기 없이 바로 공유 자원에 접근하려고 시도한다.
          컨텍스트 스위칭 하는 시간 보다 임계영역이 대기시간이 더 짧을 때 유리하다.
- **단점**
    - 무한 루프로 인한 CPU 리소스 낭비
        - 스핀락은 공유 자원이 사용 중일 때 무한 루프를 돌면서 계속해서
          검사하므로, 다른 쓰레드가 공유 자원을 해제하지 않는 경우 busy waiting으로 인해 CPU 리소스가 낭비될 수 있다.
        - 공유 자원에 대한 경쟁이 많은 경우 비효율적이며 경쟁이 강하게
          발생할 경우 스핀락을 사용하면, 대기시간이 더 길어질 수 있다.
- **싱글 코어 & 멀티 코어**
    - 싱글코어에서 스핀락을 사용하면, 해당 쓰레드가 무한 루프를 돌며 다른
      쓰레드가 CPU를 점유 할 기회를 주지 않기에 싱글코어 환경에서 일반적으로 busy waiting으로 인해 성능이 저하될 수 있으므로 멀티코어 환경에서 사용하는 것이 더 효율적이다.