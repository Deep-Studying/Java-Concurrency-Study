개요

---

- **wait(), notify(), notifyAll()**
    - **모니터 객체의 조건변수와 함께 사용해서 동기화를 구현할 수 있는 동기화 메커니즘**
    - 뮤텍스(상호배제) 동기화 기법으로 충족되지 않는 동기화 문제를 해결할 수 있는 동기화장치
    - 반드시 synchronized 블록 내에서 사용해야 하며 이는 쓰레드가 모니터락을 확보한 상태에서 해당 기능들이 작동한다는 것을 의미한다.

---

### wait()

---

- 쓰레드를 대기 상태로 전환, 모니터 락은 해제되며 다른 쓰레드가 모니터 락을 획득하여 작업 수행
- 조건 변수와 함께 사용하여 특정 조건이 만족될 때 까지 대기, 이를 통해 쓰레드간 효율적 협력 구현

---

### notify() & notifyAll()

---

- notify()
    - 같은 모니터의 조건 변수에서 대기중인 쓰레드 중 임의의 하나를 깨운다.
- notifyAll()
    - 쓰레드 전체를 깨운다.
- 어떤 쓰레드가 깨어날 것인지 알  수 없으며 무작위로 선택되기에 notifyAll()을 선호한다.
- 깨어난 쓰레드가 다시 실행되어야 할 때, 해당 객체의 모니터 락을 다시 획득하기 위해 경쟁 필요
- 메서드 호출 후 synchronized 블록이 끝나기 전 락 해제 X, 해당 블록을 빠져나가야 락이 해제된다.

---

### 기본 동작 구조

---

**wait()은 대기(락 해제) notify()는 통지 (락 획득)** 쓰레드 간 상호배제와 상호협력으로 동기화 구현가능

- **쓰레드가 동기화 영역에서 특정한 조건이 만족될 때 까지 진행못한다면 문제점이 생긴다.**
    - 락을 소유한 쓰레드가 조건 만족 까지 동기화 영역을 못 빠져나오므로 무한정 대기 상황 발생
    - 특정한 조건이 만족될 때 까지 상태 확인 (Spin Lock) 해야 하므로 자원낭비로 이어질 수 있다.

---

### wait() & notify() 기본 구현

---

```java
class SharedData {
  private boolean hasItem = false;
  
  public void consume() throws InterruptedException {
	  synchronized(lock) { // wait()은 synchronized 블록 안에서 실행하지 않으면 IllegalMonitorStateException 예외 발생
	  while (!hasItem) {
	    wait(); // 쓰레드 대기 상태 전환
    }
    hasItem = false; // 락 해제 전 상태값 초기화, 신규 진입 시 다른 쓰레드에 의해 notify 할 때 까지 대기상태
   } // 락 해제
 }
 
 public void produce() throws InterruptedException {
	 synchronized(lock) { // notify()는 synchronized 블록 내에서만 실행해야 한다.
	  hasItem = true; // 깨어난 쓰레드가 상태값 학인 시 조건이 참이되게 해서 대기에 빠지지 않도록 한다.
	  notifyAll(); // 대기중인 모든 쓰레드 깨움, 락은 가지고 있다.
   } // 락 해제
 }
}
```