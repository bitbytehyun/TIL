# Java의 멀티쓰레드
## 실제 개념

자바는 초기 버전(1.0)부터 멀티쓰레드를 지원했습니다. 이는 자바가 동시성 기반 네트워크 프로그래밍을 고려하여 설계되었기 대문입니다.  
즉, 자바는 한 프로그램 내에서 여러 작업을 동시에 처리하고자 멀티쓰레드를 초기부터 지원했습니다.

자바의 버전이 올라가면서 쓰레드를 생성하고 관리하는 방식이 점점 더 추상화되고 편리해졌습니다. 

---
### 1단계: `Thread`와 `Runnable`
초기 자바는 `Thread` 객체를 직접 생성하고, 실행 흐름을 수동으로 제어해야 했습니다. 
두 가지 방식의 생성을 지원합니다.
```java
public class MyThread extends Thread {
    public void run() {
        System.out.println("Thread running");
    }
}
// new MyThread().start()

public class MyRunnable implements Runnable {
    public void run() {
        System.out.println("Runnable running");
    }
}
// new Thread(new MyRunnable().start())
```
하지만 쓰레드를 직접 생성하는 방식은 문제점이 있습니다.
* 쓰레드는 생성마다 메모리 스택 공간을 필요로 함
* 지나치게 많은 쓰레드가 생성되면 콘텍스트 스위칭 비용이 증가
---

### 2단계: `Executor`와 `Future`
자바 5에서는 `java.util.concurrent` 패키지를 통해 `ExecutorService`가 등장하여 스레드 수를 제한하고 관리할 수 있는 쓰레드 풀이 등장했습니다.
쓰레드 풀의 종류는 다음과 같습니다.
* `newFixedThreadPool(n)`: 고정된 개수의 쓰레드를 유지
* `newCachedThreadPool()`: 필요에 따라 쓰레드를 생성하고, 유후 쓰레드는 재사용
* `newSingleThreadExecutor()`: 하나의 쓰레드만 유지하여, 순차적으로 작업 처리
* `newScheduledThreadPool(n)`: 예약 또는 주기적인 작업 처리에 사용

```java
ExecutorService executor = Executors.newFixedThreadPool(3);

for (int i = 0; i < 5; i++) {
  executor.execute(() -> {
    System.out.println("작업 " + taskId + " 시작: " + Thread.currentThread().getName());
  });
}

// 결과
// 작업 3 시작: pool-1-thread-3
// 작업 2 시작: pool-1-thread-2
// 작업 1 시작: pool-1-thread-1
// 작업 4 시작: pool-1-thread-2
```

또한, 비동기 결과 처리를 위한 `Future`를 도입했습니다.
```java
ExecutorService executor = Executors.newFixedThreadPool(2);
Future<Integer> future = executor.submit(() -> 1 + 2);
Integer result = future.get(); // 블로킹
```
`future.get()`은 결과를 기다리기 위해 블로킹되므로, 실제로는 완전한 비동기적 구조는 아닙니다. 

Future 내부 동작은 다음과 같습니다.
```java
public class SimpleFuture<T> {
    private T result;
    private boolean isDone = false;
    
    public synchronized void set(T result) {
        // 실제 작업
        this.result = result;
        this.isDone = true;
        
        notifyAll(); // waiting 된 쓰레드를 깨움
    }
    
    public synchronized T get() throws InterruptedException {
        while (!isDone) {
            wait(); // 결과가 나올 때 까지 블로킹 됨
        }
        return result;
    }
}
```
---

### 3단계: `Fork/Join`
자바 7에서는 대규모 데이터 처리 및 재귀적인 병렬처리를 위해 `ForkJoinPool`과 `RecursiveTask` 기반인 `Fork/Join` 프레임워크가 도입되었습니다.
```java
class SumTask extends RecursiveTask<Integer> {
    int[] arr;
    int start, end;
    
    protected Integer compute() {
        // 일정 범위 이상이면, 분할 후 invokeAll
    }
}
```
`Fork/Join`은 작업을 작은 단위로 나누고, 병렬로 처리한 뒤 다시 합칩니다. 이는 CPU 코어 수를 활용한 병렬 처리에 특화되어 있습니다.

### 4단계: `CompletableFuture`
자바 8에서는 `CompletableFuture`로 비동기 + 논블로킹 (콜백 기반) + 함수형 체이닝을 가능하게 했습니다.

```java
CompletableFuture.supplyAsync(() -> "Hello") // 비동기 시작
        .thenApply(s -> s + "World")         // 중간 변형 (논블로킹)
        .thenAccept(System.out::println);    // 최종 소비자 처리
```
`CompletableFuture`의 내부 구현 예시입니다.
```java
public class SimpleCompletableFuture<T> {
    private T result;
    private boolean isDone = false;
    private Consumer<T> callback;
    
    public synchronized void complete(T result) {
        // 실제 작업
        this.result = result;
        this.isDone = true;
        
        // 콜백 실행
        if (callback != null) {
            callback.accept(result);
        }
    }
    
    public synchronized void thenAccept(Consumer<T> action) { // 콜백 등록
        if (isDone) {
            actions.accept(result);
        } else {
            this.callback = action;
        }
    }
}

```

## 내가 이해한 바
## 내가 개발할 떄 신경쓰는 점
## 개선된 점
