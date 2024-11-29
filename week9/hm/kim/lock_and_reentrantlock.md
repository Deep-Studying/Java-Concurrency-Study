개요

---

Lock 구현은 synchronized 처럼 상호배제, 가시성 기능을 가진 동기화 기법

synchronized 사용은 락 획득과 해제가 블록 구조화된 방식으로 발생하도록 강제한다.

synchronized는 블록을 벗어나면 락 해제, Lock 구현은 명시적으로 락을 해제해야 한다.

---

- **FairSync (공정한 락 구현)**
- **NonFairSync (불공정한 락 구현)**

---

### ReentrantLock

---

- void lock()
    - 락이 다른 쓰레드에 의해 보유되고 있지 않다면, 락을 즉시 획득하고 보유횟수를 1로 설정
- void lockInterruptibly() throws InterruptedException
    - 현재 쓰레드가 인터럽트 되지 않는 한 락을 획득,
      다른 쓰레드에 의해 보유되지 않는다면 락을 즉시 획득하고 락의 보유 횟수를 1로 설정한다.
- boolean tryLock()
    - 호출 시점에 다른 쓰레드에 의해 보유되지 않을 때 락을 획득, 보유횟수 1 설정 후 true 반환한다.
- boolean tryLock(long time, TimeUnit unit) throws InterruptedExcpetion
    - 대기 시간 내에 다른 쓰레드에 의해 보유되지 않으면 락을 획득, 보유횟수를 1 설정 후 true 반환
- void unlock()
    - 락을 해제 시도, 해제하려면 동일 쓰레드 내 lock() 메서드가 호출 횟수와 동일한 횟수 호출 필요
- Condition newCondition()
    - 이 락 인스턴스와 함께 사용하기 위한 컨디션 인스턴스를 반환한다.

---

### Synchronized & Lock 구현 비교

---

- synchronized 구문은 락 획득, 해제가 내장되어 있어 암묵적 락이라고 한다.
- Lock은 락의 획득 및 해제를 직접 명시해서 사용함으로 명시적인 락이라고 한다.

---

### Synchronized & ReentrantLock 선택 기준

---

**ReentrantLock**

- tryLock()
    - 비차단 락 획득이 필요한 경우
- lockInterruptibly()
    - 락 획득을 시도하거나 대기하는 중에 중단이 필요한 경우

**Synchronized**

- 사용하기 더 편리하고 익숙하다.
- ReentrantLock의 기능이 필요하지 않을 경우