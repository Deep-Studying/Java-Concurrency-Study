# Thread Group

### Thread Group

- **`자바`**는 **`스레드 그룹이`**라는 **`객체`**를 통해서 여러 스레드를 **그룹화**한다.
- **`ThreadGroup`**은 스레드 집합을 나타내며 스레드 그룹에는 **다른 스레드 그룹도 포함**될 수 있다.
- **`그룹`** 내의 모든 스레드는 **한 번에 종료**되거나 **중단**될 수 있다.
- **`스레드`**는 하나의 스레드 그룹에 **`포함`**되어야 한다. 명시적으로 스레드 그룹에 포함되지 않으면 **자신을 생성한 스레드 그룹에 포함**된다.
- 일반적으로 **`main 스레드`**에서 생성하는 모든 스레드는 기본적으로 **main 스레드 그룹**에 속한다.

### Create Thread Group in JVM

- **`JVM`**이 실행되면 최상위 스레드 그룹인 **`system 스레드 그룹`**이 생성된다.
- **`JVM`** 운영에 필요한 **데몬 스레드**들을 생성해서 **system 스레드 그룹**에 **`포함`**시킨다.
- **`system 스레드 그룹`**의 하위 스레드 그룹인 **main 스레드 그룹**을 만들고 main 스레드를 **그룹**에 **포함**시킨다.

### Thread 생성시 Thread Group 명시

```java
// Thread Group을 따로 지정하지 않으면 스레드를 생성한 스레드의 그룹에 포함된다.
Thread defaultGroupThread = new Thread(new GroupRunnable(), "DefaultGroupThread");

// Thread Group을 명시적으로 지정하면 해당 그룹에 포함된다.
Thread customGroupThread = new Thread(customThreadGroup, new GroupRunnable(), "CustomGroupThread");
```

### Thread Group 생성시 부모 Thread Group 지정

```java
ThreadGroup topGroup = new ThreadGroup("최상위 스레드 그룹");
ThreadGroup subGroup = new ThreadGroup(topGroup, "하위 스레드 그룹");
ThreadGroup thirdGroup = new ThreadGroup(subGroup, "하위 -> 하위 스레드 그룹");

topGroup.list();

// 출력
java.lang.ThreadGroup[name=최상위 스레드 그룹,maxpri=10]
    java.lang.ThreadGroup[name=하위 스레드 그룹,maxpri=10]
        java.lang.ThreadGroup[name=하위 -> 하위 스레드 그룹,maxpri=10]
```

### Thread Group의 부모-자식 관계가 있을때의 Priority 우선순위

```java
public class ThreadGroupScopeExample {

    public static void main(String[] args) throws InterruptedException {
        ThreadGroup topGroup = new ThreadGroup("최상위 스레드 그룹");
        ThreadGroup subGroup = new ThreadGroup(topGroup, "하위 스레드 그룹");

        Thread topGroupThread = new Thread(topGroup, () -> {
            System.out.println("상위 스레드 그룹에서 하위 그룹의 최대 우선 순위 설정 변경 전 = [" + subGroup.getMaxPriority() + "]");
            // 여기서 변경하는 Priority는 Thread Group의 Priority이다.
            subGroup.setMaxPriority(2);
            System.out.println("상위 스레드 그룹에서 하위 그룹의 최대 우선 순위 설정 변경 후 = [" + subGroup.getMaxPriority() + "]");
        }, "상위 스레드 그룹");

        Thread subGroupThread = new Thread(subGroup, () -> {
            System.out.println("하위 스레드 그룹에서 상위 그룹의 최대 우선 순위 설정 변경 전 = [" + topGroup.getMaxPriority() + "]");
            // 여기서 변경하는 Priority는 Thread Group의 Priority이다.
            topGroup.setMaxPriority(3);
            System.out.println("하위 스레드 그룹에서 상위 그룹의 최대 우선 순위 설정 변경 후 = [" + topGroup.getMaxPriority() + "]");
        }, "하위 스레드 그룹");

        topGroupThread.start();
        subGroupThread.start();

        topGroupThread.join();
        subGroupThread.join();

        // start 한 후에는 값이 변하지 않는다.
        System.out.println(topGroupThread.getName() + " = [" + topGroupThread.getPriority() + "]");
        System.out.println(subGroupThread.getName() + " = [" + subGroupThread.getPriority() + "]");

        // 스레드 그룹에서 설정한 Priority 값과 Parent Thread의 Priority 값 중 작은 값으로 Priority는 할당된다.
        Thread userThread1 = new Thread(topGroup, () -> {}, "유저스레드 1");
        Thread userThread2 = new Thread(subGroup, () -> {}, "유저스레드 2");
       
    }
}
```
