# User Level Thread vs Kernel Level Thread

### What is Thread?

- `스레드`는 **사용자 수준** 스레드와 **커널 수준** 스레드로 나눌 수 있다.
- `사용자 수준 스레드`는 사용자 프로그램에서 관리하는 스레드다.
- 커널 수준 스레드는 **OS**에서 **관리**하는 스레드다.

### 사용자 수준 스레드 (User Level Thread)

- `사용자 수준 스레드`란 사용자 영역에서 `스레드 라이브러리(JVM 등)`에 의해 스레드의 스케쥴링 및 생명주기 관리 등을 관리되는 **스레드**다.
- 커널은 사용자 수준 **스레드**에 대해 알지 못하고 `단일 스레드 프로세스`인 것처럼 관리한다.

### 커널 수준 스레드 (Kernel Level Thread)

- `커널 수준 스레드`란 커널이 스레드와 관련된 모든 작업을 관리한다 (**PCB**와 **TCB** 관리/유지)
- `커널`은 커널 스레드의 모든 정보를 알고 있으며 커널 스레드는 **OS 스케줄러**에 의해서 스케줄링 된다.
- `CPU`는 커널 스레드의 `실행`만 담당한다.

### 멀티 스레딩 모델

- 사용자 수준 스레드는 커널 스레드와의 매핑이 필요하다.
- 사용자 수준 스레드는 세 가지 모델로 커널 수준 스레드와 매핑하여 구현한다.
    - N:1, 1:1, N:M 스레드 매핑

### 다대일 스레드 매핑 (many to one thread mapping)

- 다수의 **사용자 수준 스레드**가 커널 수준 스레드 **한 개**에 매핑하는 유형이다.
- 커널 개입 없이 사용자 **스레드**끼리 `Context-Switching`이 발생하여 **오버헤드**가 적다.
- `스케쥴링`이나 `동기화`를 위한 커널 호출이 필요하지 않아서 **오버헤드**가 적다.
- `멀티 코어`를 활용한 **병렬처리**가 불가능하다.
- **Java** 초기 버전의 **Green Thread**가 이 모델에 해당한다.

### 일대일 스레드 매핑 (one to one thread mapping)

- 사용자 수준 `스레드`와 커널 수준 `스레드`가 **일대일**로 **매핑**하는 유형이다.
- **커널**이 전체 프로세스와 스레드 정보를 **유지**해야 한다.
- `Context-Switching`시 사용자 모드에서 커널 모드로 전환해서 스케줄링 하기 때문에 **오버헤드**가 **발생**한다.
- `자원`이 한정되어 스레드를 무한정 생성할 수 없다 → **Thread Pool** 활용
- `스레드` 중 한 개가 대기 상태가 되더라도 다른 **스레드**를 **실행**할 수 있다.
- `Java Native Thread` 가 이 모델에 해당한다.

### 다대다 스레드 매핑 (many to many thread mapping)

- 여러 개의 `사용자 수준 스레드`를 같은 수 또는 그 보다 작은 수의 `커널 수준 스레드`로 **매핑**하는 **유형**이다.
- 각 커널 수준의 `스레드`가 사용자 수준 스레드 한 개 이상과 `매핑`된다.
- **다대다** 모델은 **다대일**, **일대일** 모델의 단점을 어느 정도 해결한 `모델`이다.
- **개발자**는 필요한 만큼 많은 `사용자 수준 스레드`를 생성할 수 있다.
- **커널 수준 스레드**가 멀티 프로세서에서 `병렬`로 수행이 가능하다.
- `사용자 수준 스레드`가 I/O 시스템 콜을 발생시키면 **커널**이 다른 스레드의 **수행**을 **스케줄** 할 수 있다.
