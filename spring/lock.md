# 어플리케이션의 락 종류
락이란, 하나의 프로세스 / JVM 안에서 동시 접근을 제어하는 방식입니다. 

### 1. 뮤텍스 (Mutex, Mutual Exclusion)
* 단일 스레드만 접근 가능하도록 보호
* 예시: Java의 `synchronized` 키워드, `ReentrantLock` 클래스
* 특징
    * 하나의 임계 구역에 하나의 스레드만 진입이 가능합니다.
    * 재진입 가능 여부에 따라 `Reentrant` 여부가 결정됩니다.
 
### 2. 읽기-쓰기 락
* 읽기 작업은 여러 쓰레드가 동시에 수행될 수 있지만, 쓰기 작업은 단일 쓰레드만 가능
* 예시: `ReentrantReadWriteLock`
* 특징
    * 일긱 성능이 중요한 시나리오에서 효과적입니다.
    * 읽기-쓰기 교착 상태에 주의가 필요합니다.


 ### 3. 낙관적 락
* 충돌이 드물다고 가정하고, 락을 걸지 않은 채 처리 후, 갱신 시점에 충돌 여부를 검사합니다.
* 예시:
    * JPA/Hibernate의 `@Version` 필드를 활용
    * CAS (Compare-And-Swap) 기반으로 원자 연산을 진행
* 특징
    * 충돌이 드물면 성능이 우수합니다.
    * 충돌이 잦다면 롤백이 빈번해져, 성능이 저하됩니다.
 
### 4. 비관적 락
* 충돌이 자주 일어난다고 가정하고, 리소스 접근 시점부터 락을 걸어 다른 스레드 접근을 차단합니다.
* 예시
    * DB 접근 시 `SELECT ... FOR UPDATE`
    * `ReentrantLock`을 미리 확보 후에 사용합니다.
* 특징
    * 충돌 방지는 확실합니다.
    * 불필요한 대기와 교착 상태의 위험이 있습니다.
### 5. 분산 락
* 여러 서버/프로세스에 공유하는 자원에 대해 락을 관리합니다.
* 예시
    * Redis 기반 분산락 (Redission, `SET NX/EX`)
    * ZooKeeper, Etcd 기반 락
* 특징
    * 다중 서버 환경에서 유용합니다.
    * 네트워크 파티션, 클럭 드리프트로 인한 일관성 문제에 주의해야 합니다.
### 6. 스핀락
* 락을 획득할 때까지 CPU를 점유하면서 반복적으로 시도합니다.
* 예시: Java의 `AtomicBoolean`과 CAS 루프
* 특징
    * 짧은 임계 구역에 유리합니다.
    * 긴 대기 상황에서는 CPU 낭비가 심각합니다

### 7. 세마포아
* 특정 자원의 동시 접근 허용 개수를 제한하는 락입니다.
* 예시: `Semaphore(3)` - 최대 3개의 스레드가 동시에 진입 가능합니다.
* 특징
    * connection pool, thread pool 관리에 자주 사용합니다.
    * binary semaphore는 사실상 Mutex입니다.
### 8. 조건 변수 
* 락과 함께 사용되어, 특정 조건이 만족될 떄 까지 스레드를 대기 상태로 두고, 조건이 만족되면 깨웁니다.
* 예시
    * `Object.wait() / notify()`
    * `Condition.wait() / signal()`
 
### 9. 이벤트 기반 / 논블로킹 락
* Future/Promise, Reactor, CompletableFuture같은 비동기 처리로 락 자체를 회피합니다.
* 특성
    * 스레드 대기 대신 callback, event loop를 활용합니다.
    * 고성능 I/O 처리에 적합합니다.
 

| 종류                 | 특징            | 예시                          |
| ------------------ | ------------- | --------------------------- |
| Mutex              | 하나만 접근 가능     | synchronized, ReentrantLock |
| Read-Write Lock    | 읽기 병행, 쓰기는 단일 | ReentrantReadWriteLock      |
| Optimistic Lock    | 충돌 후 검증       | JPA @Version, CAS           |
| Pessimistic Lock   | 미리 락          | SELECT FOR UPDATE           |
| Distributed Lock   | 다중 서버 자원 제어   | Redis, Zookeeper            |
| Spin Lock          | CPU 점유 반복 시도  | AtomicBoolean+CAS           |
| Semaphore          | 동시 접근 개수 제한   | new Semaphore(n)            |
| Condition Variable | 조건 만족시 대기/깨움  | wait/notify, Condition      |
| Event-driven       | 락을 피하는 방식     | CompletableFuture, Reactor  |
