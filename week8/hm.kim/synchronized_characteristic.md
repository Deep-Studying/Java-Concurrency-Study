### 재 진입성

---

- 모니터 내에서 이미 synchronized 영역에 들어간 쓰레드가 다시 같은 모니터 영역으로 들어갈 수 있는데, 이를 “모니터 재진입” 이라고 한다.
- 재진입 가능하다는 것은, 락의 획득이 호출 단위가 아닌 쓰레드 단위로 일어난다는 것을 의미하며
  이미 락을 획득한 쓰레드는 같은 락을 얻기 위해 대기할 필요 없이 synchronized 블록을 만났을 때 같은 락을 확보하고 진입한다.

```java
class Parent {
	public synchronized void method() {
	  System.out.println("Parent method");
	}
}

class Child extends Parent {
  @Override
  public synchronized void method() {
    System.out.println("Child method: before calling super");
    super.method();
    System.out.println("Child method: after calling super");
  }
}

public class MonitorReentrancyInheritanceExample {
	public static void main(String[] args) {
	  Child child = new Child();
		  child.method();
  }
}
```

- 상속하게 되면, 자식은 부모의 락과 동일한 락을 가지게 된다.
- 동기화된 메서드에 다른 동기화된 메서드를 호출하는 경우, 이미 락을 가지고 있는 쓰레드가
  같은 락을 확보하고 재진입 시 데드락이 발생하지 않고 정상적으로 진행할 수 있게 된다.

---

### 가시성

---

- synchronized는 가시성을 지원한다.
    - 한 쓰레드가 공유자원을 수정/쓰기 작업 시 다른 쓰레드가 수정한 내용이 보이는 것을 말한다.

---

### 그 외 특징

---

- sleep()을 실행한 쓰레드는 동기화 영역에서 대기중이더라도 획득한 락을 놓거나 해제하지 않는다.
- synchronized의 동기화 영역에 진입하지 못하고 대기 중인 쓰레드는 인터럽트 되지 않는다.
- synchronized의 동기화 영역에 진입하지 못하고 대기중인 쓰레드가 다시 경쟁해서 모니터를
  획득하는 것은 순서가 정해져 있지 않다.
    - 모니터를 계속 획득하지 못하는 기아 상태의 쓰레드가 나올 수 있지만 스케줄러가 조절한다.

---