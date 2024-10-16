# 스레드 생성 - Thread & Runnable

- Java Thread는 JVM의 User Thread과 Kernel Thread와 1:1 매핑되어 최종적으로 커널에 관리된다.

→  User Thread를 생성할 때 시스템 콜을 통해 커널에서 생성된 Kernel Thread를 사용한다.

- 자바에서는 Platform Thread으로 정의되어 있다.

→ OS 플랫폼에 다라 JVM이 사용자 스레드를 매핑하게 된다 .

---
## 다양한 스레드 생성 방법

1. Thread를 상속한 클래스
```java
Thread read = new WorkThread();
thread.start();
```
**가장 기본적인 방식, Thread 클래스 반드시 상속!**

**상속 특성상 컴파일 시점에 실행 코드가 결정되어 동적인 기능 변경 불가능**

2. Thread 익명 클래스
```java
new Thread(){
	@Override
	public void run(){
		//작업
	}
}.start();
```
**스레드 객체 참조하거나 재활용하지 않음, 일회용 사용일 때**
3. Runnable 구현
```java
Runnable task = new ExecuteTask();
Thread thread = new Thread(task);
thread.start();
```
**Runnable을 Task로 활용하는 방식, 선호하는 방식**

**스레드와 실행하고자 하는 Task를 분리하여 유연하고 확장 가능한 구현 가능**

4. Runnable 익명 클래스
```java
new Thread(new Runnable(){
		@Overrid
		public void run(){
			//작업
		}
	    }).start();
```
**Runnable 타입을 참조하지만, 재활용하지 않고 일회용일 때**

5. Runnable 람다 방식
```java
new Thread(() → {
		//작업
}).start();
```
**Runnable 을 람다 형식 구현, 코드 간결**