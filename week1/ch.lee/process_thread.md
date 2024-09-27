# Process & Thread

### Process

- 프로그램 데이터가 메모리에 올라와 <span style="color:red;">`CPU을 할당 받은 상태</span>
-  `OS`의 자원을 할당 받는 최소단위

`Process`가 `OS`에게 메모리를 할당받았을 때 일부(4Gb 받았다면 1Gb)는 `Kernel`에 사용하기 위해서 남겨둔다.

→  <span style="color:red;">Kernel Level Thread, User Level Thread </span>구분하여 할당하기 위함

→ 나머지로 `Process`에 대한 자원(Stack, Heap, data, Code)에 대해서 사용한다.

- `OS`는 각 `Process`마다 Stack, Heap, Code, Data의 형식을 할당

→ 독립된 메모리 영역으로 프로세스마다 영향을 받지 않고 <span style="color:red;">독립적인 작업 수행 가능</span>

→ 기본적으로 프로세스마다 통신 불가능하며 <span style="color:red;">IPC나 공유 메모리 등의 통신 기법을 이용해서 프로세스 간 통신</span>해야 한다.

---
### Thread

- 프로세스가 받은 자원을 이용하는 실행/흐름의 단위(<span style="color:red;">하나의 프로세스는 1개 이상의 Thread을 반드시 갖는다.</span>)

→ `Java`에서 Code을 실행할 때 `Main`을 호출하게 된다(즉, 실행하려면 main에 대한 Thread가 반드시 필요하다.)

- 각 필요한 `Stack`만 할당받고, Heap, Data, Code 영역은 공유해서 스레드에 할당한다.

- 스레드 간 메모리 Stack 영역에 접근하지 못하므로, 독립적인 실행 흐름을 가질 수 있어서 <span style="color:red;">독립적인 함수 호출</span>이 가능해진다.

실질적으로 `Process`에서 OS 자원을 소모하는 역할로 `CPU`의 최소 실행 단위

`Thread`의 선점이 일어날 때 CPU 실행 흐름 전환되는 <span style="color:red;">Context Switching</span>이 발생한다.

---
### Process vs Thread

**Process**

- `Process` 독립된 메모리 영역 사용(<span style="color:red;">리소스 비용 大</span>)함으로, 자식 프로세스가 죽어도 영향을 받지 않는다. → <span style="color:red;">안정성 확보</span>
- `Process`간 전환시 레지스터/캐시 메모리 초기화 등 무거운 작업 발생 → <span style="color:red;">시간적 비용 발생</span>
- `Process` 간 통신 기법이 어렵고 복잡 → <span style="color:red;">오버헤드 大</span>

**Thread**
- `Thread` 중 하나에 문제가 발생하면 <span style="color:red;">전체 프로세스에 영향</span>
- `Process` 메모리 영역 공유하기 때문에 <span style="color:red;">Context Switching 적고, 리소스를 효율적 사용</span>
- `Thread` 들이 공유 메모리 영역에 <span style="color:red;">동시 접근으로 인한 동기화 문제 발생</span>


