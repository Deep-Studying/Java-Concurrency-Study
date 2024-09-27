# Context Switch

### Context Switch

- 하나의 `CPU`는 동일한 시간에 하나의 `작업`만 `수행`할 수 있다.
- 한 프로세스에서 다른 `프로세스`로 전환해야 하는데 이것을 `컨텍스트 스위치`라고 한다.

### Context

- `프로세스` 간 전환을 위해서는 어디까지 명령을 수행했고 `CPU Register` 에는 어떤 값이 저장되어 있는지에 대한 `정보`가 `필요`하다.
- 이 정보들은 `운영체제`가 관리하는 `PCB`라고 하는 자료구조의 공간에 저장된다.

### PCB (Process Control Block)

- `프로세스`마다 유지하는 정보를 유지하는 정보를 담는 `커널` 내의 자료구조이다.
- `컨텍스트 스위칭`은 `CPU`가 프로세스 간 **PCB 정보**를 교체하고 비우는 일련의 과정이다.

### 프로세스 상태

- 프로세스는 **New**, **Ready**, **Running**, **Blocked**, **Exit** 상태를 가진다.
    - `New` : 프로세스를 **생성하고 있는 단계**로 커널 영역에 PCB가 만들어진 상태
    - `Ready` : 프로세스가 **CPU를 할당받기 위해** 기다리고 있는 상태
    - `Running` : 프로세스가 CPU를 할당받아 **명령어를 실행 중**인 상태
    - `Waiting` : 프로세스가 **I/O 작업** 완료 혹은 **사건 발생**을 기다리는 상태
    - `Terminated` : 프로세스가 **종료**된 상태

### 컨텍스트 스위칭이 일어나는 조건

- 실행 중인 `프로세스`에서 **I/O 호출**이 일어나서 프로세스 상태가 **running** 에서 **waiting** 상태로 전이된 경우
- 프로세스 **스케줄링 알고리즘**에 의해서 현재 실행중인 프로세스가 중지되고 다른 프로세스를 **실행**시켜 줘야 하는 경우

### 스레드 컨텍스트 스위칭

- `TCB` (Thread Control Block)
    - **Thread** 상태정보를 저장하는 자료구조이다.
    - **스레드**가 하나 생성될 때마다 **PCB** 내에서 **TCB**가 생성된다.
    - **컨텍스트 스위칭**이 일어나면 기존의 스레드 **TCB**를 저장하고 새로운 스레드의 **TCB**를 가져와 **실행**한다.

### Process vs Thread

- `프로세스`는 **컨텍스트 스위칭**시 메모리 주소 관련 여러가지 처리를 하기 때문에 **오버헤드**가 크다.
- `스레드`는 프로세스 내 메모리를 공유하기 때문에 `프로세스`에 비해 **오버헤드**가 작아서 **컨텍스트 스위칭**이 빠르다.
- **스레드**는 생성하는 **비용**이 커서, 많은 수의 스레드 생성으로 인해 **메모리 부족** 현상이 발생할 수 있다.
- **스레드**가 많으면 빈번한 **컨텍스트 스위칭**으로 인해 **애플리케이션**의 **성능**이 **저하**될 수 있다.