# 사용자 모드 vs 커널 모드
- `OS`는 사용자가 컴퓨터 시스템을 사용하기 쉽게  <span style="color:red;">환경 제공 및 자원 효율적 관리</span>를 해준다.
- 운영체제의 핵심적인 기능을 담당하는 부분 =  <span style="color:red;">커널</span>

  →하드웨어와 소프트웨어의 `중계자` 역할
- CPU, I/O 장치, 메모리, 저장소 같은 `하드웨어 자원을 프로그램에 할당`하는데 있다.
- 하드웨어 자원에 대한 `직접적인 접근을 방지하여 자원을 보호`

  → 응용 프로그램이 직접 조작하면 프로세스간 데이터 훼손시 컴퓨터 시스템 전체에 오류 발생

  → 하드웨어 자원 접근하려면 반드시 `운영체제를 통해서만 접근`
---
### CPU 권한 모드(사용자 모드, 커널 모드)

    사용자 모드(Mode Bit = 1) : 응용 프로그램 코드가 실행되는 모드, 메모리의 유저 영역만 접근

    → 디스크, 메모리, I/O 장치 직접 접근 X, 입출력 필요할 때에는 커널 모드 변환 필요

    커널 모드(Mode Bit = 0) : 커널 영역 코드가 실행되는 모드, 메모리의 유저 영역 및 커널 영역 모두 접근

    → 하드웨어 자원에 직접 접근 가능
---

### 시스템 호출(System Call)
- 응용 프로그램이 `OS`의 `Kernel`이 제공하는 <span style="color:red;">서비스를 이용할 수 있도록 접근하는 인터페이스</span>
- 파일 입출력, 화면에 메시지 출력 등의 기능은 커널 모드일 때 `CPU`가 실행하기 때문에 반드시 `시스템 콜을 사용해서 커널 모드를 전환해`야 한다.

시스템 호출 동작 과정
- 응용 프로그램은 작업 과정에서 커널의 기능을 사용하기 위해 `매우 빈번하게 시스템 콜을 요청`하고 `사용자 모드와 커널 모드르를 상호 전환`하며 실행된다.
- I/O 처리를 위해 사용자 모드와 커널 모드를 번갈아 오가는 것은 `Context Switching` 관련이 있으며, <span style="color:red;">멀티 스레드 환경에 참고해야 할 중요한 배경지식</span>이다.