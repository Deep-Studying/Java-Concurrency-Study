# 사용자 수준 스레드 vs 커널 수준 스레드

#### 사용자 수준 스레드

사용자 영역(사용자 프로그램)에서 Thread Library(Pthread,s, Java Thread(JVM))에 의해 <span style="color:red;">스레드의 생성과 종료, 스레드간 메시지 전달, 스레드의 스케쥴링 보관 등 모든 것을 관리</span>

  → 커널은 사용자 수준의 스레드에 대해 알지 못하며, `단일 스레드 프로세스`인 것처럼 관리

#### 커널 수준 스레드

`Kernel`이`Thread`와 관련된 <span style="color:red;">모든 작업 관리(PCB, TCB 관리 및 유지), 스레드의 모든 정보를 알고 있으며 OS 스케줄러에 의해서 스케줄링 </span>된다.

→ `CPU`는 생성된 커널 스레드의 실행만을 담당

---
### 멀티 스레딩 모델
- `CPU`는 `OS Scheduler`가 예약하는 `Kernel Thread`만 할당받아 실행시켜서, <span style="color:red;">사용자 수준 스레드는 커널 수준 스레드와 매핑이 필요</span>

   → 다대일/일대일/다대다 스레드 매핑

#### 다대일 스레드 매핑(many to one thread mapping)
다수의 사용자 수준 스레드가 `한 개의 커널 수준 스레드`에 매핑 → <span style="color:red;">사용자 수준의 스레드 모델</span>
- 커널 개입 없이(스케줄링, 동기화 등 커널을 호출 X) 스레드끼리 스위칭 발생 → <span style="color:red;">오버헤드 적다.</span>
- 단일 스레드의 프로세스 단위로 프로세서를 할당해서  <span style="color:red;">멀티코어를 활용한 병렬처리 불가능</span>
- `Block I/O` 발생 → 모든 스레드 Block, `프로세스 자체를 블록`
- 자바 초기 버전, Green Thread 모델


#### 일대일 스레드 매핑(one to one thread mapping)
사용자 수준 스레드 1개와 커널 수준 스레드 1개가 매핑 → <span style="color:red;">커널 수준의 스레드 모델</span>
- 커널이 `전체 프로세스와 스레드 정보를 유지`해야 한다. → `Context Switch`시 사용자 모드에서 커널 모드로 전환되어서 `오버헤드가 발생`
- 자원 한정으로 스레드 무한 생산 불가 → 대안으로 `스레드 풀`을 활용
- 스레드 단위로 CPU 할당 → <span style="color:red;">멀티 코어 활용한 병렬 처리 가능</span>
- 스레드 중 1개가 대기 상태여도 다른 스레드 실행 가능 → <span style="color:red;">멀티 스레드 동시성 활용 가능</span>
- 자바에서 `Native Thread`가 이 모델 사용

#### 다대다 스레드 매핑(many to many thread mapping)
- 여러 개 사용자 수준 스레드를 `같은 수 또는 그보다 작은 수`의 커널 수준 스레드로 매핑
- 각 커널 수준의 스레드가 `사용자 수준 스레드 한개 이상과 매핑`
- 다대일, 일대일 단점 어느 정도 해결 → <span style="color:red;">필요한 만큼 사용자 수준 스레드 생성, 커널 수준 스레드가 멀티 프로세서에서 병렬로 수행 가능</span>
- 사용자 수준 스레드가 I/O 시스템 콜 발생시 `커널이 다른 스레드 수행 스케줄링` 가능