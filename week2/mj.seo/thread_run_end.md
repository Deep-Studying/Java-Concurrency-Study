# 스레드 실행 및 종료 - 1

### Java Thread의 특징

- `Java Thread`는 OS **스케줄러**에 의해 실행 순서가 결정되므로 **JVM**이 제어할 수 없다.
- 새로운 스레드는 현재 `스레드`와 독립적으로 `실행`된다.
- **스레드**는 최대 한 번 시작할 수 있고 종료된 후 다시 `시작`할 수 없다.

### Java Thread 생성 과정

- `Main Thread`가 새로운 `Thread` 생성
- `Main Thread`가 start()를 호출해서 Thread 실행 시작
- 내부적으로 `native method`인 **start0()**을 호출해서 시스템 콜을 요청한다.
- **레퍼런스** → https://github.com/openjdk/jdk/blob/master/src/hotspot/share/prims/jvm.cpp
    
    ```jsx
    JVM_ENTRY(void, JVM_StartThread(JNIEnv* env, jobject jthread))
    #if INCLUDE_CDS
      if (CDSConfig::is_dumping_static_archive()) {
        // -Xshare:dump 옵션이 사용되는 동안 여러 Java 스레드가 병렬로 실행되면,
        // 심볼 및 클래스가 무작위 순서로 로드되어 결과적으로 CDS 아카이브가 비결정적(non-deterministic)이 될 수 있습니다.
        //
        // 다행히도, -Xshare:dump 옵션이 사용되는 동안에는 모듈 그래프를 생성하는 등의 작업을 
        // 수행하는 메인 Java 스레드만 실행되며(여기서 시작되지 않음),
        // ReferenceHandler, FinalizerThread, CleanerImpl 같은 클래스 정적 초기화자에 의해
        // 시작된 다른 스레드를 시작하지 않아도 안전합니다.
        if (log_is_enabled(Info, cds)) {
          ResourceMark rm;
          oop t = JNIHandles::resolve_non_null(jthread);
          log_info(cds)("JVM_StartThread() ignored: %s", t->klass()->external_name());
        }
        return;
      }
    #endif
      JavaThread *native_thread = nullptr;
    
      // 스레드를 시작하는 동안 예외를 던지면, 
      // 잠금(lock) 순위 문제로 인해 Threads_lock을 잡고 있을 수 없습니다.
      // 예를 들어, 예외를 생성하는 동안 Heap_lock을 잡아야 할 수 있습니다.
      bool throw_illegal_thread_state = false;
    
      // Thread::start에서 jvmti 이벤트를 발생시키기 전에 Threads_lock을 해제해야 합니다.
      {
        ConditionalMutexLocker throttle_ml(ThreadsLockThrottle_lock, UseThreadsLockThrottleLock);
        // C++ Thread 및 OSThread 구조체가 해제되지 않도록 보장합니다.
        MutexLocker ml(Threads_lock);
    
        // JDK 5부터 java.lang.Thread의 threadStatus가 스레드 재시작을 방지하기 위해 사용되므로,
        // 일반적으로 JavaThread가 null이어야 합니다.
        // 그러나 JNI로 첨부된 스레드의 경우, Thread 객체가 생성되고
        // threadStatus가 업데이트되기 전까지의 작은 시간 창이 있어 이를 확인해야 합니다.
        if (java_lang_Thread::thread(JNIHandles::resolve_non_null(jthread)) != nullptr) {
          throw_illegal_thread_state = true;
        } else {
          jlong size =
                 java_lang_Thread::stackSize(JNIHandles::resolve_non_null(jthread));
          // C++ Thread 구조체를 할당하고 네이티브 스레드를 생성합니다.
          // 자바에서 가져온 스택 크기는 64비트 부호가 있는 값이지만,
          // 생성자는 size_t(부호 없는 타입)를 사용하므로 플랫폼에 따라 32비트 또는 64비트일 수 있습니다.
          //  - 32비트 플랫폼에서 size가 UINT_MAX를 초과할 경우 잘리는 것을 방지합니다.
          //  - 음수를 전달하는 것을 방지하여 매우 큰 스택이 생성되는 것을 막습니다.
          NOT_LP64(if (size > SIZE_MAX) size = SIZE_MAX;)
          size_t sz = size > 0 ? (size_t) size : 0;
          native_thread = new JavaThread(&thread_entry, sz);
    
          // 이 시점에서 메모리 부족으로 인해 JavaThread에 대한 osthread가 생성되지 않았을 수 있습니다.
          // 이 상황을 확인하고 필요시 예외를 던집니다.
          if (native_thread->osthread() != nullptr) {
            // 현재 스레드는 "prepare" 내에서 사용되지 않음을 주의하세요.
            native_thread->prepare(jthread);
          }
        }
      }
    
      if (throw_illegal_thread_state) {
        THROW(vmSymbols::java_lang_IllegalThreadStateException());
      }
    
      assert(native_thread != nullptr, "스레드를 null로 시작하려고 하나요?");
    
      if (native_thread->osthread() == nullptr) {
        ResourceMark rm(thread);
        log_warning(os, thread)("java.lang.Thread \"%s\"에 대해 네이티브 스레드 시작 실패",
                                JavaThread::name_for(JNIHandles::resolve_non_null(jthread)));
        // 'native_thread'에 대한 참조를 갖고 있는 이는 없어야 합니다.
        native_thread->smr_delete();
        if (JvmtiExport::should_post_resource_exhausted()) {
          JvmtiExport::post_resource_exhausted(
            JVMTI_RESOURCE_EXHAUSTED_OOM_ERROR | JVMTI_RESOURCE_EXHAUSTED_THREADS,
            os::native_thread_creation_failed_msg());
        }
        THROW_MSG(vmSymbols::java_lang_OutOfMemoryError(),
                  os::native_thread_creation_failed_msg());
      }
    
      JFR_ONLY(Jfr::on_java_thread_start(thread, native_thread);)
    
      // 네이티브 스레드를 시작합니다.
      Thread::start(native_thread);
    
    JVM_END
    
    ```
    
- 커널 스레드가 생성되고 `자바 스레드`와 **1:1 매핑**된다.
- 커널 스레드는 `OS 스케줄러`부터 CPU 할당 받기 전에는 **실행대기** 상태이다.
- `커널 스레드`가 실행상태가 되면 **JVM** 에서 매핑된 자바 스레드의 `run() 메서드`를 호출한다.

### Thread 실행

- `Thread`가 실행되면 **run()** 메서드가 호출된다.
- `Runnable` 구현체가 존재하면 `Runnable`의 **run()**을 실행하게 된다.

`start()`가 아닌 `run()` 메서드를 직접 호출하면 직접 호출한 **Thread**의 실행 스택에서 `run()`이 실행된다.

즉, `run()` 메서드를 직접 호출하면 **Thread가 생성되지 않는다.**

### Thread Stack

- `Thread`가 생성되면 생성된다.
- `Stack`은 각 `Thread` 마다 독립적으로 할당되어 작동하고, **Thread** 간 접근하거나 공유할 수 없다.
- 스택은 `OS`에 따라 크키가 주어지고, 크기를 넘기면 `StackOverFlowError` 가 발생한다.

### Stack 구성 정보

- `Stack`에 대한 메모리 접근은 `LIFO 순서`로 이루어지고 `Stack`은 프레임으로 구성된다.
- 프레임은 새 `메서드`를 호출할 때마다 **로컬 변수** 및 객체 **참조 변수**와 함께 `Stack`의 맨 위에 생성된다.

### Stack 메모리 상태 관리

- `Stack` 내부의 변수는 변수를 **생성한 메서드**가 실행되는 동안에만 존재한다.
- `Stack` 메모리에 대한 접근 속도는 `Heap 메모리`에 접근하는 것보다 **빠르다.**

### Thread 종료

- `Thread`는 **run() 메서드**의 코드가 모두 실행되면 `자동`으로 `종료`된다.
- `Thread`는 예외가 발생하면 `종료`되며 다른 `Thread`에 영향을 미치지 않는다.
- 어플리케이션은 `Single Thread`인 경우와 `Multi Thread`인 경우 종료 기준이 다르다.

### Single Thread Application

- `Single Thread`는 사용자 스레드가 없는 `Main Thread`만 존재한다.
- `Main Thread`만 종료되면 애플리케이션이 **종료**된다.

### Multi Thread Application

- `Multi Thread`인 경우 `JVM` 에서 실행하고 있는 모든 **Thread**가 종료되어야 애플리케이션이 **종료**된다.
- 각 `Thread`의 종료 시점은 **처리 시간** 및 **OS 스케줄링**에 따라서 매번 다르게 나올 수 있다.
