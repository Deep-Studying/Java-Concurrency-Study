# 스레드 생성

### Java Thread

- 자바 스레드 생성시 `JVM`에서 **시스템 콜**을 수행한다.
- `User Thread`는 시스템 콜을 통해서 `커널`에서 생성된 `Kernel Thread`와 **1:1 매핑**하기 때문에 커널에서 관리된다.
- `자바`에서는 **Platform Thread**로 정의되어 있고, `OS`에 따라 **JVM**이 사용자 스레드를 매핑한다.

### Thread API - https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/lang/Thread.html

### Thread 생성

`Thread`를 생성하는 방법은 두 가지가 있다.

- **`Thread`** 클래스 상속받기

```jsx
public class WorkerThread extends Thread {
	@Override
	public void run() { // 작업 내용 }
}

Thread thread = new WorkerThread();
thread.start();
```

- **`Runnable`** 인터페이스를 구현하기

```jsx
public class ExecuteTask implements Runnable {
	@Override
	public void run() { // 작업 내용 }
}

Runnable task = new ExecuteTask();
Thread thread = new Thread(task);
thread.start();
```

작업내용을 `Runnable` 인터페이스를 구현한 클래스에 정의하고 그 **인스턴스**를 `Thread` 생성시 전달하면 스레드는 `Runnable`을 실행한다.
