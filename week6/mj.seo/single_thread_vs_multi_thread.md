# Single Thread vs Multi Thread

### Single Thread ? Multi Thread ? 선택 기준

- **`프로세스`**는 **싱글 스레드** 프로세스와 **멀티 스레드** 프로세스로 나뉜다.
- 어떤 방식이 **`자원`**을 더 효율적으로 사용하고 **`성능`**이 좋은지 **충분한 검토** 후 **결정**해야 한다.

### Single Thread

- **`장점`**
    - Context-Switching 없음
    - 동기화 이슈가 없음
    - 자원 비용이 적음
    - 프로그래밍 난이도가 낮음
- **`단점`**
    - CPU 멀티코어 활용 못함
    - 순차적 실행으로 **`응답성`** 및 **`전체 처리량`**이 낮음
    - **`I/O(입출력)`** 처리시 CPU가 낭비된다
    - **`스레드`**에 **오류**가 발생하면 프로그램이 **종료**된다.

### Multi Thread

- **`장점`**
    - **동시성**으로 사용자의 **응답성** 향상
    - **`CPU 멀티코어`**의 **병렬성 활용**으로 성능 향상
    - **`CPU`** 낭비 없는 **자원**의 **효율적**인 사용
    - 한 `스레드 오류`는 다른 **스레드**에 **영향**이 없음

- **`단점`**
    - 빈번한 **`Context-Switching`** 으로 인한 **성능 저하**
    - **동기화 이슈**가 있음
    - **`스레드 생성 비용`**이 적지 않음
    - **`프로그래밍 난이도`**가 높음

- **`정리`**
    - 대부분의 **`CPU`**는 멀티 코어를 지원하므로, **병렬적 성능** 및 **동시적 자원 사용** 관점에서는 **`멀티 스레드`**가 유리하다.
    - **`싱글 스레드`**나 아주 **`적은 스레드`**를 활용한 **`Non-Blocking`** 프로그래밍이 **`멀티 스레드`** 프로그램보다 **더 높은 응답성**을 보여주는 경우도 있다.

### MultiThreading & Concurrency

- 여러 **`스레드`**가 **읽기** 및 **쓰기** 작업을 **`같은 메모리 영역`**에서 **동시**에 **실행**할 경우 **`동시성 문제`**가 발생한다.
- **`동시성 문제`**는 **싱글 스레드**에서는 절대 발생하지 않는다.