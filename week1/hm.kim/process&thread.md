# Process & Thread

### Process

- 운영체제로부터 자원을 할당받은 **최소 작업 단위**
- 프로세스마다 독립된 메모리영역을 **Code/Data/Stack/Heap** 형식으로 할당해준다.
- 프로그램 파일을 실행하여 데이터들이 메모리에 올라와 **CPU를 할당 받고 명령을 수행하고 있는 상태**
- 독립된 메모리 영역을 할당해 주기 때문에 프로세스 간 상호 영향을 받지 않고 **독립적인 작업**을 수행한다.
- 기본적으로 다른 프로세스의 변수나 자료에 접근할 수 없고 필요 시 **IPC, 공유메모리** 등의 통신 기법 사용

### Thread

- 프로세스가 운영체제로부터 할당받은 자원을 이용하는 실행단위로 반드시 1개 이상의 쓰레드를 갖는다.
- 프로세스 내에서 필요한 **Stack**만 할당받고 **Code/Data/Heap** 영역은 각 쓰레드가 공유하여 사용한다.
- 쓰레드 간 메모리 스택 영역에 접근할 수 없으므로 **독립적인 실행 흐름**을 가지고 **독립적인 함수 호출** 가능

### Thread & CPU

- 쓰레드는 운영체제의 스케줄러에 의해 관리되는 CPU의 최소 실행 단위
- 스케줄러의 알고리즘에 따라 프로세스에 속한 한개의 쓰레드가 선점되어 CPU에 할당된다.
- 쓰레드 간의 선점이 일어날 때 CPU의 실행 흐름(문맥)이 전환되는 **컨텍스트 스위칭**이 발생한다.

### Process vs Thread

- Process
    - 프로세스 간 통신 기법이 어렵고 복잡해서 통신으로 인한 **오버헤드가 크다.**
    - 프로세스 생성 시 독립적으로 메모리가 할당되기 때문에 리소스 비용이 크다.
    - 여러개의 자식 중, 하나의 프로세스에 문제가 발생하여도 다른 프로세스에게 영향을 미치지 않는다.
    - 프로세스 간 전환(**컨텍스트 스위칭**) 시 **레지스터, 케시메모리 초기화** 등 시간적인 비용이 많이 발생

- Thread
    - 쓰레드 간 통신비용이 적어 통신으로 인한 **오버헤드**가 작다.
    - 하나의 쓰레드에서 **오류가 발생할 경우**, 프로세스 자체가 **종료**될 수 있다.
    - 쓰레드 간 공유 메모리 영역의 동시 접근으로 인한 **동기화 문제**가 발생한다.
    - 여러개의 쓰레드 중 **하나**라도 문제가 발생하면 **전체 프로세스**에 영향을 미친다.
    - 프로세스의 메모리 영역을 공유하기에 컨텍스트 스위칭 시간이 적고 **리소스 사용**이 **효율**적이다.