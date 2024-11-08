### 안전한 쓰레드 구성 

개요

---

여러 쓰레드에서 클래스나 객체에 동시 접근해서 실행하여도 정확성이 보장되는 코드 → (thread-safe)

기본적으로 쓰레드 안정성을 헤치는 코드나 상태를 가지고 있지 않으면 쓰레드에 안전하다 할 수 있다.

다수의 쓰레드가 공유 자원에 쓰기 작업을 시도할 때 발생하기에 어떤 자원을 공유하는지 알아야 한다.

---

### 쓰레드에 안전한 구조

---

- **임계영역을 동기화한다.**
    - 동시에 **여러개의 쓰레드가 임계영역을 접근하지 못하도록** **락 매커니즘**을 사용한다.
- **동기화 도구 사용**
    - **세마포어,CAS,Atomic 변수, 동시성 자료구조 등**의 동기화 도구 사용하여 안전성 구현
- **쓰레드의 스택에 한정해서 상태 관리**
    - 쓰레드마다 할당된 스택 메모리 내에서 상태를 관리, **다른 쓰레드와 상태를 공유할 수 없게** 한다.
- **ThreadLocal 사용**
    - 쓰레드마다 가지고 있는 전용 저장소인 ThreadLocal을 사용해 상태를 관리, **상태공유 불가**
- **불변 객체 사용**
    - 상태를 변경할 수 없게 하거나 클래스를 설계할 때 상태를 변경할 수 없도록 만든다.

---

### 동기화 도구 사용

---

- Non Thread-safe
    - 각 쓰레드가 **동시**에 **동일**한 Item을 **중복**해서 읽어 올 수 있다.
- Thread-safe
    - 각 쓰레드가 **대기**하고 있다가 **순차**적으로 Item을 읽어온다.

---

### 쓰레드의 스택 한정

---

- **지역 변수**
    - 기본형 지역변수는 쓰레드마다 독립적으로 가지고 있는 스택에 저장되기에 공유 불가
    - 메서드로 전달되는 기본형 파라미터 변수도 스택에서만 관리되므로 쓰레드에 안전하다.
- **지역 객체 참조**
    - 객체 참조변수와 다른데, 객체는 스택에 저장되지 않고 메모리의 힙 영역에 저장된다.
    - 지역적으로 생성된 객체가 해당 메서드에서 **벗어나지 않는다면** 쓰레드에서 안전하다.
    - 다른 클래스의 메소드에 파라미터로 넘겼을 때, **멤버 변수**로 저장하면 **안전하지 않다.**
    - 문자열 같이 **불변 객체**는 상태가 **변경되지 않기때문에** 쓰레드에 안전하다.

```java
public void someMethod(String name, int age) {

    /** 메서드 안에서 객체를 생성하면, 힙에는 각 쓰레드의 객체가 생성되어
     참조변수로 할당되기 때문에 쓰레드에 안전하다. **/
    Member member = new Member(name, age);

    // 문자열은 불변 객체이기 때문에 쓰레드에 안전하다. -> 정말?
    String dept = "영업부";
}
```

- **멤버 변수 참조**
    - 쓰레드마다 객체를 생성하는 원리는 동일, 쓰레드의 스택별로 객체가 생성되어 참조되도록 구현
    - 아래의 코드는, Member가 멤버변수로 정의되어 있어 쓰레드에 안전하지 않은 클래스이다.
    - **객체를 항상 새로 생성한다 → 쓰레드에 안전하다, 멤버에 접근할 수 있다 → 안전하지 않다.**

```java
public class Company(String name, int age) {

	Member member = new Member(name, age) ;
		
		public void changeName(String name) {
			member.setName(name);
		}
	}

public class MyRunnable implements Runnable {
	Company company = null;
	
		public MyRunnable(Company company) {
			this.company = company;
		}
		
		public void run() {
			this.company.changeName("someName");
		}
}
```

---

### **불변 객체 사용**

---

- Setter가 없음, 생성자에서 멤버 변수 초기화. 멤버 변수는 final로 선언 등 불변객체를 생성한다.
- 불변객체는 어떠한 상황에서도 상태가 변하지 않기에 쓰레드에서 안전하다고 할 수 있다.

```java
public class Immutable {

  private final int value1;
  private final String value2;
  
  private ImmutableValue(int value1, String value2) {
    this.value1 = value1;
    this.value2 = value2;
  }
  
  public int getValue1() {
    return this.value1;
  }
  
  public String getValue2() {
	  return this.value2;
  }
  
  public Immutable newImmutable(int value) {
		  new Immutable(this.value1 +value, this.value2);
	}
}
// 최초로 객체가 생성되는 시점 이후로 값이 변하지 않는다.
```