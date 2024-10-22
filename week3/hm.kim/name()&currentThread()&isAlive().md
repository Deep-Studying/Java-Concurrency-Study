### Thread Name

멀티 쓰레드 환경에서 어떤 쓰레드가 실행중인지 확인할 때 쓰레드에 이름을 지정하면 쉽게 찾을 수 있다.

자바에서 쓰레드가 생성되면, 쓰레드 이름이 자동으로 주어진다. 가장 먼저 생성되는 메인 쓰레드 (main)

쓰레드 이름은 Thread-0, Thread-1, Thread-2, .. Thread-n 과 같이 0부터 순차적으로 증가한다.

---

- 쓰레드 이름을 정하는 두 가지 방법
    - 쓰레드 객체 생성 시 인자로 전달
        - Thread myThread = new Thread([ThreadGroup], [Runnable], “myThread”);
        - setName(String name)으로 설정
            - myThread.setName(”myThread”);
        - getName() 으로 쓰레드 이름 참조
            - myThread.getName();
---

### CurrentThread
- Thread 클래스의 정적 메서드로 현재 실행 중인 쓰레드 개체에 대한 참조를 반환한다.
- ex) Thread.currentThread().getName(), if(Thread.currentThread() == thread)
---

### isAlive

- 쓰레드가 살아있는지에 대한 여부를 확인할 수 있다.
- 쓰레드의 start() 메서드가 호출되고, 종료되지 않은 경우 활성 상태로 간주되어 true를 반환한다.
---