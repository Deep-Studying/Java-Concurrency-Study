### 세마포어


개요

---

- 공유자원에 대한 접근을 제어하기 위해 사용되는 신호 전달 매커니즘 동기화 도구
- 자바에서는 `java.util.concurrent` 패키지에서 세마포어 구현체를 포함하고 있다.

---

### 세마포어의 작동방식

구분

---

| S | P (Wait) | V (Signal) |
| --- | --- | --- |
| 정수형 변수 | S가 1 감소 (S—) | S가 1 증가 (S++) |
| 공유자원의 개수 
(이 개수만큼 쓰레드 접근 허용) | 쓰레드가 임계구역에 진입하기 전
실행되어 카운트의 값을 1 감소시킨다. | 쓰레드가 임계구역에서 빠져나올 때
실행되어 카운트 값을 1 증가시킨다. |

S가 0보다 크면 공유자원 접근 허용, S가 0이면 공유자원 접근이 거부된다.

`* 같은 세마포어의 P,V 함수의 S 연산은 여러 쓰레드 간 동시에 실행되지 않도록 원자적 실행 보장 필요`

---

### 세마포어 유형

- 카운트 변수 S가 1인 이진 세마포어(Binary Semaphore)
- 2 이상의 양수값을 가진 카운팅 세마포어(Counting Semaphore)

---

### 뮤텍스와 세마포어

- 동작방식
    - 뮤텍스는 공유자원에 대한 접근을 동시에 하나의 쓰레드만 가능하도록 보장
    - 세마포어는 카운팅 기법으로 특정개수의 쓰레드가 동시에 공유자원에 접근할 수 있도록 제어
- 소유권
    - 뮤텍스는 소유권이 있어 락을 획득한 쓰레드만이 락을 해제할 수 있다.
    - 세마포어는 소유권이 없어 특정 개수의 쓰레드가 동시에 접근을 허용하는 카운팅 기법으로 동작
- 초기값
    - 뮤텍스는 기본적으로 잠겨있는 상태로 시작한다.
    - 세마포어는 초기값 설정 가능, 값에따라 처음부터 쓰레드가 자원에 접근할 수 있는지 여부 결정
- 사용목적
    - 뮤텍스는 상호배제를 위해 사용, 하나의 자원에 하나의 쓰레드가 접근하도록 보장해야 하는 경우
    - 세마포어는 리소스의 한정적 사용을 제어하는데 사용되며 특정
      개수의 쓰레드만이 동시에 자원에 접근하도록 제한하고자 하는경우