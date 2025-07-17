# 자바 Enum의 개념과 활용
## 실제 개념
개발을 하다 보면 일정한 **상태나 값들이 고정된 형태로 열거**되어야 할 때가 많습니다.  
예를 들어, **결제 상태**는 `대기`, `완료`, `실패`, 또는 **회원 등급**은 `일반`, `실버`, `골드`처럼 미리 정의된 값만 사용해야 할 수 있습니다.

자바에서는 예전부터 이런 열거값들을 **`public static final` 상수**로 선언해 관리해왔습니다.

```java
public class PaymentStatus {
    public static final String PENDING = "PENDING";
    public static final String COMPLETED = "COMPLETED";
    public static final String FAILED = "FAILED";
}
```

하지만 이런 방식은 단점이 있습니다.

```java
public void handle(String status) {
    if (PaymentStatus.PENDING.equals(status)) {
        System.out.println("승인 대기");
    } else if (PaymentStatus.COMPLETED.equals(status)) {
        System.out.println("결제 완료");
    } else {
        System.out.println("기타 상태");
    }
}

handle("Pendding"); // 오타지만 컴파일 오류 없음!
handle("취소됨");     // 정의되지 않은 값도 통과!
```

---

## 🧩 자바 Enum의 등장

자바 5부터 `enum` 키워드가 도입되어 **컴파일 시점에 타입 안정성을 보장**하면서  
**상수들을 객체처럼 다룰 수 있는** 기능이 추가되었습니다.

```java
public enum PaymentStatus {
    PENDING,
    COMPLETED,
    FAILED
}
```

```java
public void handle(PaymentStatus status) {
    switch (status) {
        case PENDING -> System.out.println("승인 대기");
        case COMPLETED -> System.out.println("결제 완료");
        case FAILED -> System.out.println("결제 실패");
    }
}
```

---

## 🔍 Enum 내부 구조 이해

자바의 모든 enum은 내부적으로 `java.lang.Enum` 클래스를 상속받으며,  
실제로 다음과 같이 **클래스와 유사한 구조로 컴파일**됩니다:

```java
public final class Day extends Enum<Day> {
    public static final Day MONDAY = new Day("MONDAY", 0);
    public static final Day TUESDAY = new Day("TUESDAY", 1);
    public static final Day WEDNESDAY = new Day("WEDNESDAY", 2);
    ...
}
```

- 싱글톤 객체로 생성되어 static 필드에 저장됨
- 생성자는 `private`
- 메서드: `ordinal()`, `name()`, `values()` 등 제공

---

## 🧱 필드와 메서드 추가

```java
public enum Direction {
    NORTH(0), SOUTH(180), EAST(90), WEST(270);

    private final int degrees;

    Direction(int degrees) {
        this.degrees = degrees;
    }

    public int getDegrees() {
        return degrees;
    }
}
```

```java
public Direction opposite() {
    return switch (this) {
        case NORTH -> SOUTH;
        case SOUTH -> NORTH;
        case EAST  -> WEST;
        case WEST  -> EAST;
    };
}
```

### 상수별 다른 동작

```java
public enum Operation {
    PLUS {
        public int apply(int x, int y) { return x + y; }
    },
    MINUS {
        public int apply(int x, int y) { return x - y; }
    };

    public abstract int apply(int x, int y);
}
```

---

## ⚙️ 실전에서의 활용

### ✅ 전략 패턴 대체

```java
public enum OrderStatus {
    ORDERED("주문됨"),
    SHIPPED("배송중"),
    DELIVERED("배송완료"),
    CANCELED("취소됨");

    private final String description;

    OrderStatus(String description) {
        this.description = description;
    }

    public String getDescription() {
        return description;
    }
}
```

---

### ✅ EnumSet

```java
EnumSet<Day> weekend = EnumSet.of(Day.SATURDAY, Day.SUNDAY);

if (weekend.contains(Day.SATURDAY)) {
    System.out.println("주말입니다.");
}
```

- 내부는 `long` 비트마스크 기반
- 빠른 검색, 메모리 효율 우수

---

### ✅ EnumMap

```java
EnumMap<PaymentStatus, String> messages = new EnumMap<>(PaymentStatus.class);
messages.put(PaymentStatus.PENDING, "대기 중");
messages.put(PaymentStatus.COMPLETED, "완료됨");
```

- 내부적으로 `ordinal()`을 키로 사용
- 배열 기반으로 `O(1)` 성능

---

### ✅ 스프링 빈으로 활용

```java
@Configuration
public class HandlerConfig {
    @Bean
    public EnumMap<Status, Handler> handlerMap(...) {
        return new EnumMap<>(...); // 전략 주입
    }
}
```

`EnumMap<Enum, 전략 Bean>`으로 스위치 없이 상태 로직 분기 가능

---

## ✅ 정리: enum의 장점

| 장점 | 설명 |
|------|------|
| 타입 안전성 | 컴파일러가 오타/유효성 검사 |
| 가독성 | 의미 있는 값 명시 |
| 객체처럼 사용 | 필드, 메서드, 인터페이스 구현 가능 |
| 메타 정보 제공 | name(), ordinal(), values() 등 |
| 고성능 컬렉션 | EnumSet, EnumMap |
| 싱글톤 보장 | 정적 객체, private 생성자 기반 |

---

## 🧩 마무리

`enum`은 단순히 상수를 모아놓은 것이 아니라,  
**로직을 담을 수 있는 객체로서의 상수**입니다.

실무에서 **상태**, **전략**, **권한**, **설정 값**처럼 제한된 값을 표현할 땐  
`enum`이 타입 안전성과 구조적 명확성, 성능까지 모두 잡을 수 있는 최선의 선택입니다.
