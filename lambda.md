# 람다

## 실제 개념
람다는 자바 8에서 새롭게 등장한 문법입니다. 람다로 익명함수를 간결하게 표현할 수 있습니다.

자바는 객체지향이기 때문에, 단순한 동작 하나도 클래스나 객체로 표현해야 하는 번거로움이 있습니다. 이러한 문제를 해결하기 위해 하나의 동작만 정의할 수 있는 인터페이스, 즉 함수형 인터페이스가 등장하게 되었습니다.
람다는 이 함수형 인터페이스를 훨씬 간단하게 구현할 수 있게 해줍니다.
```java
@FunctionalInterface
interface Runnable {
  void run();
}

// 람다 없이 익명 클래스 사용
Runnable r = new Runnable() {
  @Override
  public void run() {
    System.out.println("직접 동작");
  }
}

// 람다 사용
Runnable r2 = () -> System.out.println("람다 동작");
```
(함수형 인터페이스는 오직 하나의 추상 매서드만 갖는 인터페이스며, `@FunctionalInterface` 어노테이션을 붙이면 컴파일러가 이를 확인합니다.)

자바의 주요 함수형 인터페이스는 `Comparator`, `Predicate`, `Consumer`, `Function`, `Suplier` 등이 있고, 람다로 이들의 구현체를 간결하게 표현함으로써 Stream API 또는 Optional, CompletableFuture 등과 함께 효과적으로 쓸 수 있습니다. 
```java
list.stream()
  .filter(n -> n % 2 == 0)
  .forEach(System.out::println);
```

## 내가 이해한 바


## 내가 개발할 때 신경쓰는 점
## 개선된 점
