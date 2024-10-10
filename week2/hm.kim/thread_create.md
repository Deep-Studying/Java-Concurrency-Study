# 쓰레드 생성 
생성 방법은 크게 두 가지 형태로 구현 할 수 있다.

- Thread 클래스를 상속하는 방법
- Thread 클래스 상속 → run () 오버라이드 → start () 실행
- 작업내용을 쓰레드 내부에서 직접 재정의해서 실행한다.
- Runnable 인터페이스를 구현하는 방법
- Runnable  구현 → run () 오버라이드 → Thread  생성 시 생성자 전달 → start () 실행
- 작업내용을 Runnable에 정의해서 쓰레드에 전달하면 쓰레드는 Runnable을 실행한다.
- 다양한 쓰레드 생성 패턴
- Thread를 상속한 클래스
- 기본적인 방식이며 반드시 상속받아야 한다. 동적인 기능 변경이 불가능하다.

```
Thread thread = new WorkThread();
thread.start();
```

- Thread 익명 클래스
- 쓰레드 객체를 참조하거나 재활용하지 않고 일회용으로 사용하는 경우

```
new Thread() {
@Override
  public void run() {
  // code
  }
}).start()
```

- Runnable 구현
- 태스크로 활용하는 방식, 쓰레드와 실행하고자 하는 태스크를 분리해서 유연한 구조로 구현

```
Runnable task = new ExecuteTask();
Thread thread = new Thread(task);
thread.start();
```

- Runnable 익명 클래스
- Runnable 타입을 참조하거나 재활용 하지 않고 일회용으로 사용하는 경우

```
new Thread(new Runnable() {
@Override
public void run() {
// code
}
}).start()
```

- Runnable 람다 방식

```
new Thread(() -> {
// code
}).start()
```