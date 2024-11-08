# Thread API - sleep()

- 지정된 시간동안 현재 스레드의 실행을 일시 정지(`대기 상태`)

    → 시간이 지나면 실행 대기 상태로 전환


- 네이티브 메서드로 연결되며 시스템 콜을 통해 커널모드에서 수행 후 유저 모드로 전환

   → `Context Switching` 유발

---
## sleep() In JAVA


```java
// 지정된 밀리초만큼 스레드 수면 상태
// 밀리초에 대해서 음수가 될 수 없으므로 음수이면 IllegalArgumentException 발생
static sleep(long millis) throws InterruptedException
```

```java
// 지정된 밀리초 + 나노 초 동안 스레드 수면 상태
// 나노초 범위(0 ~ 999999)
static sleep(long millis, int nanos) InterruptedException
```

### sleep(0), sleep(n)
- `sleep(0)` : 현재 스레드와 동일한 우선순위를 가진 다른 스레드에게 실행 기회를 주기 위해 사용

    →동일한 우선순위를 가진 스레드가 없으면 현재 스레드에게 CPU을 계속 할당(ContextSwitching X)
- `sleep(n)` : 조건에 상관없이 현재 스레드를 대기상태로 두고 다른 스레드에 CPU를 할당(모드 전환 및 ContextSwitching 발생)

  → 다른 스레드에게 명확하게 실행을 양보하기 위해서는 `sleep(0)`보다는 `sleep(1)`을 사용해야 한다.
### sleep() 작동 방식

- sleep() 되면 OS 스케줄러는 현재 스레드를 지정된 시간 동안 대기 상태로 전환 → 다른 스레드 혹은 프로세스게 CPU 사용
- 대기 시간이 종료 → 스레드는 실행 대기 상태로 전환
- 실행 상태가 되면 스레드는 남은 지점부터 실행을 다시 실행
- 동기화 메서드 영역에 수면 중인 스레드는 획득한 모니터나 락을 잃지 않고 계속 유지한다. 
   
   → wait()은 모니터나 락을 놓아버리지만 sleep()는 놓지 않는 차이점이 있다.
- sleep() 중인 스레드에게 인터럽트 발생시 현재 스레드 대기 해제되고 실행상태로 전환되어 예외를 처리
- **스레드의 수면 시간은 OS 스케줄러 및 시스템 기능에 따라 제한되어 정확성을 보장하지 않으며, 시스템 부하가 많고 적음에 따라 지정한 수면 시간과 차이가 날 수 있다.**

---
## InterruptedException
- 스레드가 수면 중에 인터럽트 될 경우 `InterruptedException` 발생
- 다른 스레드는 잠자고 있는 스레드에게 인터럽트, 즉 중단(멈춤)신호 보낼 수 있다.
- `InterruptedException` 예외 시, 스레드는 수면 상태에서 깨어나서 실행 대기 상태로 전환된다.

```java
[InterruptedException] 발생!
interrupt() → sleep()
sleep() → interrupt()
        
[Example]
try {
        Thread.sleep(3000);
} catch (InterruptedException e) {

}
```