# Thread API - interrupt()

### what is interrupt() ?

- `interrupt`는 어떤 주체의 행동이나 **실행 흐름**을 **방해**한다는 **의미**다.
- `interrupt()`는 특정한 스레드에게 `interrupt` 신호를 줌으로서 스레드의 실행을 **중단**하거나, **작업 취소**, **강제 종료** 등으로 사용할 수 있다.

### interrupt() detail information

- `interrupt()`는 **스레드**에게 인터럽트가 발생했다는 신호를 보낸다.
- `interrupt()`는 **스레드**가 현재 **실행 흐름**을 멈추고 `인터럽트 이벤트`를 **먼저 처리**하도록 한다.
- `interrupted 속성`
    - **스레드**는 인터럽트 상태를 **내부 필드(interrupted)**로 가지고 있고, 기본 값은 **false** 이다.
    - `인터럽트`된 스레드가 처리해야 하는 **특별한 규칙**이나 **정해진 동작**은 없다.
    - 한 **스레드**가 `다른 스레드`를 **인터럽트** 할 수 있고 **자기 자신**도 **인터럽트** 할 수 있다.
    - `interrupt()` 하는 **횟수**는 **제한**이 없다.

### Interrupt 상태 확인 방법

- `static boolean interrupted()`
    - 스레드의 **인터럽트 상태**를 **반환**하는 `static method`
    - 현재 **인터럽트** 상태가 `true`인 경우 **true**로 반환하고, `인터럽트` 상태를 **false로 초기화**한다.
    - `인터럽트`를 **해제**하는 경우 다른 곳에서 `스레드`에 대한 **인터럽트 상태**를 체크하는 곳이 있다면 **별도의 처리**가 **필요**할 수 있다.
    - `인터럽트`를 **강제**로 **해제**하기 때문에 **다시 인터럽트**를 걸 수 있다.
- `boolean isInterrupted()`
    - 스레드의 **인터럽트 상태**를 **반환**하는 `Instance method`
    - `스레드`의 **인터럽트 상태**를 변경하지 않는다.
    - `인터럽트 상태`를 `확인`하는 용도로만 사용할 경우 **이 메서드를 사용**하는 것이 좋다.

### InterruptedException

- `InterruptedException`은 **interrupt()** 메커니즘의 일부이며 `대기`나, `차단` 등 상태에 있거나 `blocking` 상태를 만나는 시점의 스레드에 **interrupt** 할때 **발생**한다.
- `InterruptedException`이 발생하면 **인터럽트 상**태는 자동으로 **초기화**된다.
- 다른 곳에서 `인터럽트` 상태를 **참조**하고 있다면 `예외 구문`에서 해당 스레드에 다시 **interrupt()** 해야 할 수 있다.
- `InterruptedException`이 발생하는 케이스
    - Thread.**sleep()**, Thread**.join()**, Object.**wait()**
    - Future.**get()**, BlockingQueue.**take()**
