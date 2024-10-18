### 개요

---

- Interrupt는 방해하다 라는 뜻으로, 어떤 주체의 행동 및 실행흐름을 방해한다는 의미
- 자바 쓰레드에서는 특정한 쓰레드에게 인터럽트 신호를 알려줌으로
  쓰레드의 실행을 중단하거나 작업 취소, 강제 종료 등에서 사용할 수 있다.

---

### Interrupt()

---

- interrupt()는 쓰레드에게 인터럽트가 발생했다는 신호를 보내는 메커니즘
- interrupt()는 쓰레드가 현재 실행 흐름을 멈추고 인터럽트 이벤트를 처리하게 시그널을 보내는 장치
    - **iterrupted 속성**
    - 쓰레드는 인터럽트 상태로 알려진 **iterrupted**를 가지고 **인터럽트 발생 여부를 확인할 수 있다.**
      기본값은 false이다.
    - 인터럽드된 쓰레드가 처리해야 할 특별한 규칙이나 기준은 없으나, 일반적으로 인터럽트 상태
      를 사용해서 **쓰레드를 중지하거나, 작업을 취소하거나, 종료하는 등의 기능을 구현할 수 있다.**
    - 한 쓰레드가 **다른 쓰레드를 인터럽트 할 수 있고 자기 자신을 인터럽트 할 수도 있다.**
    - interrupt() 하는 횟수는 제한이 없으며, 인터럽트 할 때 마다 쓰레드의 상태를 true로 변경한다.

---

### 인터럽트 상태 확인 방법

---

- **static boolean interrupted()**
    - 쓰레드의 인터럽트 상태를 반환하는 정적 메서드
    - **만약 현재 인터럽트 상태가 true인 경우 true를 반환 후 false로 초기화하므로 해제하는 역할**
    - 인터럽트를 해제하는 경우 다른곳에서 쓰레드에 대한
      **인터럽트 상태를 체크하는 곳이 있다면, 별도의 처리가 필요할 수 있다.**
    - 인터럽트를 **강제로 해제했기에 다시 인터럽트를 걸어서 인터럽트 상태를 유지할 수 있다.**

---

- **boolean isInterrupted()**
    - 쓰레드의 인터럽트 상태를 반환하는 인스턴스 메서드이다.
    - 이 메서드는 쓰레드의 인터럽트 상태를 변경하지 않고 계속 유지한다.
    - 인터럽트 상태를 확인하는 용도로만 사용할 경우, Interrupted() 보다 이 메서드를 사용하자.

---

### InterruptedException

---

- InterruptedException은 interrupt() 메커니즘의 일부이며 대기, 차단 등
  블록킹 상태이거나 블록킹 상태를 만나는 시점의 쓰레드에 인터럽트 할 때 발생하는 예외이다.
- InterruptedException이 발생하면 인터럽트 상태는 자동으로 초기화된다.
- 다른곳에서 인터럽트 상태를 참조한다면, 예외 구문에서 대상 쓰레드에  interrupt() 해야할 수 있다.
- InterruptexException이 발생하는 케이스
    - Thread.sleep(), Thread.join(), Object.wait()
    - Future.get(), BlockingQueue.take()

    ```
    Thread-1 ----> Thread-2 ---->  Interrupted(true) -> sleep(), join(), wait() -> InterruptedException -> Interrupted(false) -> thread2.interrupt()
    interrupt()  Interrupted(false) 인터럽트 걸림                                      예외처리 필요                 인터럽트 해제         필요 시 해제 인터럽트 상태 원복
    ```