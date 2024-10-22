### UncaughtExceptionHandler

개요

---

- 기본적으로 쓰레드의 run()은 예외를 던질 수 없기에  run() 안에서 예외를 처리해야 한다.
- RuntimeException 타입의 예외가 발생하여도 쓰레드 밖에서 예외를 캐치할 수 없고 사라진다.
- 쓰레드가 비정상적으로 종료되었거나 특정한 예외를 쓰레드 외부에서 캐치하기 위해서 자바에서는
  UncaughtExceptionHandler 인터페이스를 제공한다.

---

UncaughtExceptionHandler

---

- 캐치되지 않는 예외에 의해 Thread가 갑자기 종료했을 때에 호출되는 **핸들러 인터페이스**
- 어떤 원인으로 인해 쓰레드가 종료되었는지 대상 쓰레드와 예외를 확인해서 파악할 수 있다.

---

Thread API

---

- static void setDefaultUncaughtExceptionHandler
    - 모든 쓰레드에서 발생하는 uncaughtException을 처리하는 정적 메서드
- void setUncaughtExceptionHandler(UncaughtExceptionHandler ueh)
    - 대상 쓰레드에서 발생하는 uncaughtException을 처리하는 인스턴스 메서드
    - setDefaultUncaughtExceptionHandler 보다 우선순위가 높다.

---

전체적인 흐름도

---

- 쓰레드 외부

    ```
    Thread --- setUncaughtExceptionHandler --- UncaughtExceptionHandler
    
    start()
    ---------------------------------------------------------------------
    	⬇️
    	
    Runnable
    
    run()
    
      ⬇️                throw exception                 ⬆️
    uncaughtException        ➡️            dispatchUncaughtException
    ```

- 쓰레드 내부