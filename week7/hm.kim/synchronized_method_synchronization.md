### synchronized 메소드 동기화 

### 인스턴스 메소드 동기화

---

- 인스턴스 단위로 모니터 동작, 동일한 인스턴스 안 synchronized가 적용된 곳은 하나의 락을 공유
- 인스턴스가 여러개일 경우, 각 모니터 객체를 가지므로 쓰레드는
  모니터별로 락을 획득, 동기화 영역 에 진입하고 빠져나올 때 락을 해제할 수 있다.

---

### 정적 메소드 동기화

---

- 클래스 단위로 모니터가 동작, synchronized가 적용된 곳은 하나의 락을 공유한다.
- 인스턴스와 별개의 모니터를 가지고 임계영역을 동기화하기에 락은 클래스 단위로 쓰레드 간 공유
- 클래스는 메모리에 오직 하나만 존재, 하나의 모니터를 공유해서 동기화 하려 할 때 사용할 수 있다.

---

### 인스턴스 + 정적 메소드 동기화

---

- 혼용되었을 경우, 각 모니터별로 동기화를 진행한다.
- 모니터가 섞여있기에 동기화가 의도한대로 동작하는지 주의가 필요하다.