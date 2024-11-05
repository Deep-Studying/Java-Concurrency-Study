# Thread 예외처리 - UncaughtExceptionHandler

### Thread의 예외

- **`Thread`**의 **`run()`**은 예외를 던질 수 없기 때문에 `예외`가 발생하면 **run()** 안에서 **처리**해야 한다.
- **`Thread`**가 **비정상**적으로 **종료**되거나, 특정한 예외를 **Thread** 외부에서 처리하기 위해서 **`UncaughtExceptionHandler`** 인터페이스를 제공한다.

### UncaughtExceptionHandler

- 어떤 원인으로 인해 **`Thread`**가 **종료**되었는지 대상 **`스레드`**와 **`예외`**를 파악할 수 있다.
- **예외**가 발생하면 **`uncaughtException`**이 호출된다.
- **`void uncaughtException(Thread t, Thorwable e)`**

### Thread Exception API

- **`static void setDefaultUncaughtExceptionHandler`**
    - 모든 스레드에서 발생하는 **`uncaughtException`**을 **처리**한다.

- **`void setUncaughtExceptionHandler(UncaughtExceptionHandler ueh)`**
    - 대상 스레드에서 발생하는 **`uncaughtException`**을 **처리**한다.
    - setDefaultUncaughtExceptionHandler 보다 **우선순위**가 높다.

```java
 public static void main(String[] args) {
        try {
            new Thread(() -> {
                throw new RuntimeException("스레드 예외 발생");
            }).start();
        } catch (Exception e) { // 예외는 잡히지 않는다.
            e.printStackTrace();
        }
}
```
