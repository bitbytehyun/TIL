# 동기화
## 실제 개념
-- 락 (임계영역), 동기화 기법에 대해 개념과 자바 발전

멀티쓰레드 환경에서는 여러 쓰레드가 공유 메모리 영역에 접근합니다.
이때, 두개 이상의 쓰레드가 동시에 같은 자원에 접근하면 예상치 못한 결과가 발생하는 경쟁 조건(Race Condition)이 생길 수 있습니다.

특히 힙 메모리에 저장된 객체는 공유 대상이 되며, 객체 상태를 변경하는 코드는 반드시 임계 영역으로 간주해야 합니다.
이 임계 영역에 동시에 진입하지 못하도록 막는 기술이 동기화입니다.

동기화를 통해, 다음을 얻을 수 있습니다.
* 한 번에 하나의 쓰레드만 임계 영역에 진입하도록 제한
* 데이터의 무결성 보장
* 쓰레드 안전성을 확보

자바에서 구현된 동기화 방식들은 다음과 같습니다.
### 1. `synchronized` 키워드
자바에서 가장 기본적인 동기화 기법입니다. JVM 수준의 모니터 락을 사용하며, 매서드, 블록, static 영역에 지정할 수 있습니다.
```java
public class Counter {
    private int count = 0;
    
    public synchronized void increment() {
        count++;
    }
    
    public void decrement() {
        synchronized (this) {
            count--;
        }
    }
}
```
`synchronized` 블록은 해당 객체의 모니터 락 (Object Monitor)을 획득해야만 진입이 가능합니다. 
JVM은 객체에 대한 ObjectMonitor 구조체를 유지하며, 객체에 접근 가능한 쓰레드를 소유자, 대기 큐, 진입 대기 큐로 관리합니다.

JVM 내부에서는 Biased Locking, Lightweight Lock, Heavyweight Lock 등 단계적 최적화를 통해 성능을 높입니다. (???)
### 2. `ReentrantLock` 클래스
자바 5부터 등장한 `ReentrantLock`은 `synchronized` 보다 더 세밀한 락 제어가 가능하도록 만든 자바 레벨의 락입니다.
```java
public class Counter {
    private final ReentrantLock lock = new ReentrantLock();
    private int count = 0;
    
    public void increment() {
        lock.lock(); // 명시적으로 락 획득
        try {
            count++;
        } finally {
            lock.unlock(); // 반드시 락 해제
        }
    }
}
```
다음을 제공하여 장점을 갖고 있습니다. (????) 
* 공정성 옵션 (`new ReentrantLock(true)`)
* 조건 변수 사용 가능 (`Condition`)
* 시도 기반 락 획득 (`tryLock`)
* 재진입 가능 (Recursive Lock)
### 3. `volatile` 키워드

### 4. `Atomic` 클래스
자바 5부터 등장하여, 락을 사용하지 않는 CAS 라는 하드웨어 수준(cpu 명령어)의 원자성 연산을 사용합니다.

```java
public class Counter {
    private AtomicInteger counter = new AtomicInteger();
    
    public void increment() {
        counter.incrementAndGet(); // 내부적인 CAS 사용
    }
}
```
내부적으로는 CAS 명령어를 사용하여 락보다 빠르고 가볍지만, 복잡한 조건부 로직에는 적합하지 않습니다.


- 락을 얼마나 알아야 하는건지? Atomic, ReentrantLock, ReadWriteLock, StampedLock, 편향/경량 락, ??


## 내가 이해한 바
## 내가 개발할 때 신경 쓰는 점
## 개선된 점
