개요

---

- **무한 반복**이나 **지속 실행 쓰레드**를 `중지 또는 종료`할 수 있는 API 사용 불가 **(suspend(), stop())**
- 쓰레드를 종료하는 방법은`플래그 변수를 사용하거나 Interrupt()를 활용해서 구현`할 수 있다.

---

Flag Variable →  좀 더 자세하게 정리

---

- 플래그 변수의 값이 어떤 조건에 만족할 경우, 쓰레드의 실행을 중지하는 방식
- 플래그 변수는 동시성 문제로 가능한 atomic 변수나 volatile 키워드를 사용한다.

---

InterruptedException

---

- 대기중인 쓰레드에 interrupt() 하게 되면, InterruptedException
  예외가 발생하고 이 예외 구문에서 적절한 종료 기능을 구현할 수 있다.
    - thread.interrupt();
        - 인터럽트가 발생하면 InterruptedException 예외가
          발생하고, 예외 구문에서 반복문을 빠져나오면서 쓰레드는 종료된다.
        - 인터럽트 상태는 해제된다.