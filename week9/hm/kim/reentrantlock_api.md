락 획득 과정에서 쓰레드가 대기하거나 차단하지 않는 API를 지원하여 유연한 코드 구현 가능

**try Lock()**

```java
Thread thread1 = new Thread(() -> {
    try {
        // 락 획득 여부 즉시 반환, 실패하더라도 쓰레드가 or 차단되지 않음.
        if (lock.tryLock()) {
            System.out.println("Lock acquired");
        } else {
            System.out.println("Lock not acquired");
            //락 획득하지 못했을 경우 별도 처리
        }
    } finally {
        lock.unlock(); // finally에서 락 해제
        System.out.println("Lock released");
    }
});
```

**lockInterruptibly()**

```java
Thread thread1 = new Thread(() -> {
  try {
  // 락 획득을 시도하며 인터럽트에 의해 중단 가능
		 lock.lockInterruptibly();
		 System.out.println("Lock acquired");
 } catch(InterruptedExcepton e) {
    // 락 시도중 인터럽트에 걸리면 예외구분에서 별도처리
    System.out.println("Interrupted while acquiring lock");
 } finally {
   lock.unlock();
 }
});

Thread thread2 = new Thread(() -> {
  try {
  Thread.sleep(1000);
  // 다른 쓰레드를 인터럽트 하여 락 획득 시도를 중단시킴
  thread1.interrupt();
  } catch (InterruptedException e) {
    e.printStackTrace();
  }
});
```

**tryLock(long time, TimeUnit unit)**

```java 
Thread thread1 = new Thread(() -> {
    try {
        //최대 2초 동안 락 획득 시도, 시간 경과 시 락 획득 실패 및 false 반환
        if (lock.tryLock(2, TimeUnit.SECONDS)) {
            try {
                System.out.println("Lock acquired");
            } finally {
                lock.unlock();
                System.out.println("Lock released");
            }
        } else {
            System.out.println("Could not acquire lock within 2 seconds");
            // 락 획득 못했을 시 별도 처리 
        }
    } catch (InterruptedException e) {
        // 락 시도 중 인터럽트 걸리면 예외구문에서 별도 처리
        e.printStackTrace();
    }
});
```