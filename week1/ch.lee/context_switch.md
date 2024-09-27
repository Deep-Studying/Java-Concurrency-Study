# Context Switch

- 하나의 `CPU`에서 여러 프로세스를 `동시성`으로 처리하기 위해서  <span style="color:red;">다른 프로세스로 전환</span>하는 것
---
### Context(문맥)
- `Switch`을 위해서 이전에 어디까지 명령을 수행했고, `CPU Register`에는 어떤 값이 저장되어있는 정보가 필요
- `Context`는 `CPU`가 <span style="color:red;"> 해당 Process 실행하기 위한 Process Data</span>를 의미, `OS`가 관리하는 `PCB`라고 하는 자료구조 공간에 저장된다.

---
### PCB(Process Control Block)
- `OS`가 시스템 내의 `Process`들을 관리하기 위해 프로세스마다 유지하는 정보를 담는  <span style="color:red;">커널 내의 자료구조</span>
- `Context Switch`는 `CPU`가 프로세스 간  <span style="color:red;">PCB 정보를 교체하고 캐시를 비우는 일련의 과정</span>

---
### 프로세스 상태
- New(생성) : 프로세스를 `생성하고 있는 단계`, 커널 영역에 PCB가 만들어진 상태
- Ready(준비) : 프로세스가 CPU 할당을 받기 위해 `기다리는 상태`
- Running(실행) : 프로세스가 CPU를 할당받아 명령어를 `실행 중인 상태`
- Waiting/Blocked(대기) : 프로세스가 I/O 작업 오나료 혹은 사건 발생을 `기다리는 상태`
- Terminated/Exited(종료) : 프로세스가 `종료`된 상태

---
### 일어나는 조건
- 실행중인 `Process`에서  `I/O` 작업이 끝날 때까지  <span style="color:red;">프로세스 상태가 Running → Waiting 전이된 경우</span>
- `OS`의  <span style="color:red;">CPU 스케줄러 알고리즘에 의해 현재 실행중인 프로세스가 사용할 수 있는 시간 자원을 모두 사용</span>했을 때, 프로세스를 중지(Ready 상태 전이)하고 다른 프로세스를 실행

---
### 스레드 컨텍스트 스위칭
TCB(Thread Control Block)
- <span style="color:red;">Thread 상태 정보를 저장하는 자료 구조</span>, PC와 Register Set(CPU 정보), 그리고 <span style="color:red;">PCB를 가리키는 포인터</span>을 가진다.
- 스레드가 생성될 때마다 `PCB` 내에서 `TCB`가 생성되며 <span style="color:red;">컨텍스트 스위칭이 일어나면 기존 스레드 TCB를 저장하고 새로운 스레드 TCB를 가져와 실행</span>한다.

---
### 프로세스 vs 스레드
- `Process`가 `Context Switch`을 할 떄 <span style="color:red;">메모리 주소 관련 처리(CPU 캐시 초기화, TLB 초기화, MMU 주소 체계 수정 등)를 하기에 오버헤드가 크다.</span>
- `Thread`는 메모리를 공유하기 때문에 메모리 주소 관련 추가적인 작업이 없다.  → 프로세스에 비해 <span style="color:red;">오버헤드가 작아서 Context Switch가 빠르다.</span>
- `Thread`는 생성하는 비용이 커서 <span style="color:red;">많은 수의 스레드 생성은 Memory Leak 발생하거나 빈번한 컨텍스트 스위칭으로 인해 성능 저하가 발생</span>할 수 있다.

### 예제 코드 결과.
```
Thread 3: 0
Thread 2: 0
Thread 1: 0
Thread 3: 1
Thread 1: 1
Thread 2: 1
Thread 3: 2
Thread 1: 2
Thread 2: 2
Thread 3: 3
Thread 1: 3
Thread 2: 3
Thread 3: 4
Thread 2: 4
Thread 1: 4
```
- `Thread`을 3개로 유지하였을 때에는 병렬적으로 잘 실행되는 것을 확인할 수 있다.
```
Thread 8: 0
Thread 3: 0
Thread 11: 0
Thread 12: 0
Thread 2: 0
Thread 9: 0
Thread 10: 0
Thread 5: 0
Thread 7: 0
Thread 6: 0
Thread 4: 0
Thread 1: 0
Thread 8: 1
Thread 3: 1
Thread 11: 1
Thread 2: 1
Thread 9: 1
Thread 12: 1
Thread 7: 1
Thread 5: 1
Thread 1: 1
Thread 10: 1
Thread 4: 1
Thread 6: 1
Thread 12: 2
Thread 8: 2
Thread 3: 2
Thread 11: 2
Thread 1: 2
Thread 10: 2
Thread 9: 2
Thread 2: 2
Thread 11: 3
Thread 8: 3
Thread 9: 3
Thread 10: 3
Thread 1: 3
Thread 3: 3
Thread 12: 3
Thread 6: 2
Thread 4: 2
Thread 5: 2
Thread 7: 2
Thread 2: 3
Thread 10: 4
Thread 3: 4
Thread 12: 4
Thread 4: 3
Thread 11: 4
Thread 8: 4
Thread 6: 3
Thread 9: 4
Thread 1: 4
Thread 7: 3
Thread 5: 3
Thread 2: 4
Thread 4: 4
Thread 7: 4
Thread 5: 4
Thread 6: 4
```
- `Thread`을 12개(테스트 환경 코어 개수 : 8개)으로 실행하였을 때에는 Context Switch 발생으로 순서가 보장되지 않는 것을 확인할 수 있습니다.