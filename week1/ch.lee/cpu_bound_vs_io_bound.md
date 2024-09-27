# CPU Bound vs I/O Bound

- `Process`는 <span style="color:red;">CPU 작업과 I/O 작업의 연속된 흐름</span>

  → Context Switching 이 발생하여 <span style="color:red;">Running → Waiting</span>

  → 특정한 `Task`가 완료될 때까지 계속 반복한다.

---
### Burst
- 한 작업을 짧은 시간동안 집중적으로 연속해서 처리하거나 실행하는 것

`CPU Burst` : CPU 연속적인 실행구간, `CPU` 명령어를 실행하는 소비 시간, <span style="color:red;">Running 상태</span>

`I/O Burst`:  I/O를 연속적인 실행 구간, `I/O` 요청 완료 기다리는 시간, <span style="color:red;">Waiting 상태</span>

- `Process`마다 `CPU Burst, I/O Burst`가 차지하는 비율은 균일 X

  → 비율을 기준으로 <span style="color:red;">CPU Bound Process, I/O Bound Process</span>로 나눌 수 있다.

---
### CPU Bound Process
- <span style="color:red;">CPU Burst 작업이 많은 Process</span>, `I/O Burst`가 거의 없는 경우
- 머신러닝, 블록체인, 동영상 편집 프로그램 등 `CPU 연산 위주 작업`
- 멀티 코어의 `병렬성 최대 활용`하여 `처리 성능 극대화`한 스레드 운용

  → CPU 코어 수와 스레드 수의 비율을 비슷하게 설정한다.

---
### I/O Bound Process
- <span style="color:red;">I/O Burst 빈번히 발생하는 Process</span>, `CPU Burst`가 매우 짧다.
- 파일, 키보드, DB, 네트워크 등 `외부 연결이나 입출력 장치와의 통신 작업`에 많은 경우 해당
- CPU 코어가 많을 경우 `멀티 스레드의 동시성을 최대한 활용`하여 CPU가 Idle 상태가 되지 않도록` 최적화된 스레드 수를 운용`하여 <span style="color:red;">CPU 효율적 사용을 극대화</span>
