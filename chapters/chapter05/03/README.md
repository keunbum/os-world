# 05-03 임계 구역 해결 방법

---

## 1 


---

## 2


### 2.1




### 2.2 

### 2.3 
### 2.4

---

## 3


---

## 4 


### 4.1 

### 4.3 

---

## 5 세마포어

세마포어(semaphore)는 왜 이름이 **semaphore**인가?

> The word “semaphore” comes from the Greek words “σῆμα” (sema), meaning sign, and “φέρω” (phero), meaning to bear or carry. In its original usage, a semaphore was a system of visual signals used to communicate over long distances1. In computer science, a semaphore is a variable or abstract data type used to control access to a common resource by multiple threads and avoid critical section problems in a concurrent system such as a multitasking operating system2. The name likely comes from the analogy of using signals to coordinate access to shared resources.

굳이 직역하자면 '신호를 운반하다'?

세마포어는 어떻게 작동하는가?

> A semaphore is a signaling mechanism used to coordinate the activities of multiple processes or threads in a computer system. It uses two atomic operations: wait (also called P) and signal (also called V) for process synchronization. A semaphore either allows or disallows access to a shared resource, depending on how it is set up.
> 
> When a thread wants to access a shared resource protected by a semaphore, it performs a wait operation on the semaphore. If the value of the semaphore is greater than zero, it decrements the value and continues execution. If the value of the semaphore is zero, it blocks until another thread performs a signal operation on the semaphore. When a thread finishes using a shared resource protected by a semaphore, it performs a signal operation on the semaphore to increment its value and potentially unblock another waiting thread.

기본적으로 세마포어는 신호 메커니즘이고,  
뮤텍스는 잠금 메커니즘이다.

세마포어 초기화 후,  
프로세스는 임계 구역 진입 전, 다른 프로세스들에게  
대기(P)하라고 스위치를 사용 중으로 놓는다.  

임계 구역으로 진입한 프로세스가 작업을 마치면,  
세마포어는 비었다는 의미로 다음 프로세스에 동기화 신호(V)를 보낸다.

세마포어는 임계 구역이 잠겼는지 직접 점검하거나, busy wait을 하거나,  
다른 프로세스에 동기화 메시지를 보낼 필요가 없다.

간단한 의사코드를 작성해보면...

```c
Semaphore(n);

P();

V();
```

이는 아래와 같이 구현된다.

```c
RS = n;

if RS > 0 then RS = RS - 1;
else block();

RS = RS + 1;
wake_up();
```

세마포어 큐에 저장되어 있던 프로세스들은 wake_up()을 호출하면 모두 깨어나고,  
그중 하나가 (여러 개일수도 있음) 임계구역으로 진입하고 나머지는 다시 큐로 들어가고 깨어나길 기다린다.



---


## 6 모니터

모니터란 무엇인가?

> In concurrent programming, a monitor is a synchronization construct that allows threads to have both mutual exclusion and the ability to wait (block) for a certain condition to become false. Monitors also have a mechanism for signaling other threads that their condition has been met. A monitor consists of a mutex (lock) object and condition variables.
>
> Monitors were created to overcome problems caused by semaphores such as timing errors. Monitors are abstract data types and contain shared data variables and procedures.

이렇게 근사한 세마포어도 문제가 없는 건 아니다.  
프로그래머가 실수할 여지가 다분하다.

공유 자원을 사용할 때 모든 프로세스가 세마포어를 따른다면  
굳이 P()와 V()를 사용할 필요가 없다. 이를 실제로 구현한 것이 '모니터'이다.

모니터는 공유 자원을 내부적으로 숨기고 공유 자원에 접근하기 위한  
인터페이스만 제공함으로써 자원을 보호하고 프로세스 간에 동기화를 시킨다.

이런 면에서 운영체제의 시스템 콜과 유사하다.  

1. 임계 구역으로 지정된 변수나 자원에 접근하고자 하는 프로세스는  
   직접 P()나 V()를 사용하지 않고 모니터에 작업 요청을 한다.

2. 모니터는 요청 받은 작업을 모니터 큐에 저장한 후 순서대로 처리하고  
   그 결과만 해당 프로세스에 알려준다.


모니터는 다른 스레드에게 어떻게 신호를 보내는가? (심오한 내용이긴 하다.. 추상적?)

> Monitors have a mechanism for signaling other threads that their condition has been met. A monitor consists of a mutex (lock) object and condition variables. A condition variable is essentially a container of threads that are waiting for a certain condition.
>
> While a thread is waiting on a condition variable, that thread is not considered to occupy the monitor, and so other threads may enter the monitor to change the monitor’s state. In most types of monitors, these other threads may signal the condition variable to indicate that assertion P is true in the current state.

---

## 번외1 뮤텍스

그럼 뮤텍스란 무엇인가?

> A mutex (short for “mutual exclusion”) is a synchronization primitive that provides mutual exclusion access to a shared resource. Mutexes are used to prevent data inconsistencies due to race conditions between threads.

어떻게 작동하는가?

> A mutex works by acting as a gatekeeper to a section of code, allowing one thread in and blocking access to all others. This ensures that the code being controlled will only be hit by a single thread at a time.
> 
> A thread calls the WaitOne method of a mutex to request ownership. The call blocks until the mutex is available or until an optional timeout interval elapses. The state of a mutex is signaled if no thread owns it. A thread releases a mutex by calling its ReleaseMutex method.


---

## 번외2 아토믹 타입

Atomic type은 언제 사용하는 것이 좋을까?

> Atomic types and operations can be used to prevent race conditions by ensuring that certain operations are performed atomically, meaning that they are completed in a single step without being interrupted by other threads. This can be a useful alternative to using mutexes or semaphores in some cases.
>
> However, atomic types and operations have their limitations. They are typically only suitable for simple operations such as incrementing or decrementing a counter. For more complex operations involving multiple steps or multiple variables, mutexes or semaphores may be more appropriate.
>
> In general, whether to use atomic types or synchronization primitives like mutexes and semaphores depends on the specific use case. Atomic types can be faster and more efficient for simple operations, while synchronization primitives provide more flexibility for complex operations.


간단 예시 코드(참고만 하기)

```c
#include <stdatomic.h>
#include <stdio.h>
#include <pthread.h>

atomic_int counter = 0;

void *increment(void *arg)
{
    for (int i = 0; i < 1000000; i++)
    {
        atomic_fetch_add(&counter, 1);
    }
    return NULL;
}

int main()
{
    pthread_t thread1, thread2;

    // Increment the counter in two threads
    pthread_create(&thread1, NULL, increment, NULL);
    pthread_create(&thread2, NULL, increment, NULL);

    // Wait for both threads to finish
    pthread_join(thread1, NULL);
    pthread_join(thread2, NULL);

    printf("Counter: %d\n", counter);

    return 0;
}
```