# 멀티쓰레드란
## 실제 개념
멀티쓰레드는 하나의 프로세스 내에서 여러 작업을 동시에 실행하기 위한 기술입니다. 이를 통해 서버의 처리량과 응답성을 높일 수 있습니다.

자바는 1.0부터 멀티쓰레드를 지원했고, 이후 버전에서 점점 더 고수준의 추상화를 제공하며 발전해왔습니다.

---
### 1단계: `Thread`와 `Runnable` - 직접 쓰레드 제어
`Thread` 객체를 직접 만들고, 실행(`start()`), 종료 대기(`join()`), 일시정지(`sleep()`) 등의 매서드를 직접 호출하여 쓰레드를 관리합니다.
`Thread` 상속과 `Runnable` 구현 후 `Thread`에 전달하는 두 가지 방식의 생성을 지원합니다.
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
하지만 쓰레드를 직접 생성하는 방식은 다음 문제점이 있습니다.
* 직접 관리해야 해서 복잡함
* 쓰레드 과도 생성 시 메모리와 컨텍스트 스위칭 비용 발생---

### 2단계: `Executor`와 `Future` - 쓰레드 풀 등장
자바 5에서는 `ExecutorService`를 통해 쓰레드 풀 관리를 할 수 있습니다. 작업은 `submit()`으로 제출하고, 결과는 `Future`로 받을 수 있습니다.
```java
ExecutorService executor = Executors.newFixedThreadPool(3);
Future<Integer> result = executor.submit(() -> 1 + 2);
System.out.println(result.get()); // 블로킹
```
---

### 3단계: `Fork/Join` - 병렬 계산 최적화
자바 7에서는 큰 작업을 재귀적으로 분할하여 여러 CPU 코어에서 동시에 처리할 수 있습니다. `ForkJoinPool`과 `RecursiveTask` 을 함께 사용합니다.
```java
class SumTask extends RecursiveTask<Integer> {
    private Integer compute() {
        if (작으면) return 계산;
        else {
            invokeAll(작업1, 작업2);
            return 작업1.join() + 작업2.join();
        }
    }
}
```

### 4단계: `CompletableFuture` - 비동기와 논블로킹
자바 8에서는 `CompletableFuture.supplyAsync()`로 비동기를 시작하고, `thenApply`, `thenAccept`로 함수형 체이닝을 만들 수 있습니다. 내부적으로 `ForkJoinPool.commonPool()`을 사용합니다. (별도 `Executor`에 할당할 수 있습니다.)

```java
CompletableFuture.supplyAsync(() -> "Hello") // 비동기 시작
        .thenApply(s -> s + "World")         // 중간 변형 (논블로킹)
        .thenAccept(System.out::println);    // 최종 소비자 처리
```


## 내가 이해한 바
## 내가 개발할 떄 신경쓰는 점
## 개선된 점
