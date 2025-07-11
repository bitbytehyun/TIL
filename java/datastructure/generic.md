# 제네릭
## 실제 개념

제네릭은 컬렉션 등에서 데이터를 사용할 때 **매번 명시적 형변환을 해야하는 불편함과 타입 안정성 부족** 문제를 해결하기 위해 자바 5버전에서 도입되었습니다.   
제네릭을 사용하면 컴파일 타임에 타입을 체크할 수 있어, 불필요한 형변환을 줄이고, 코드의 가독성과 안정성을 높일 수 있습니다.   
예전에는 모든 데이터를 `Object`로 저장했기 떄문에, 데이터를 꺼낼 때 반드시 형변환이 필요했습니다.

```java
List list = new ArrayList<>();
list.add("hello");
String s = (String) list.get(0); // 형변환 필요
```
제네릭을 사용하면, 다음과 같이 바뀝니다. 
```java
List<String> list = new ArrayList<>();
list.add("hello");
String s = list.get(0); // 형변환 없이 안전하게 사용 가능
```

자바는 하위 호환성을 유지하기 위해, 제네릭 타입 정보를 컴파일 타임에만 사용하고, **런타임에는 제거**합니다.  
즉, `List<string>`과 `List<Integer>`는 컴파일 후에는 동일한 클래스로 취급됩니다. 
```java
public class Box<T> {
  private T data;
  public void set(T data) { this.data = data; }
  public T get() { return data; }
}

// 컴파일 후
public class Box {
  private Object data;
  public void set(Object data) { this.data = data; }
  public Object get() { return data; }
}
```
이렇게 타입 정보가 전부 소거되기 떄문에, `new T[]`, `T.class`, `instanceof List<T>` 등은 사용할 수 없습니다.

### 활용
제네릭은 클래스와 매서드에 사용할 수 있고,
```java
public class ApiResponse<T> {
  private T data;
  private boolean success;
}

public class ArrayUtils {
  public static <T> void printArray(T[] array) {
    for (T element: array) {
      System.out.println(element);
    }
  }
}
```
두가지 이상의 타입에도 다중 타입 파라미터로 사용 가능합니다.
```java
public class Pair<K, V> {
  private K key;
  private V value;
}
```
또한, 유연한 타입 사용을 위해 **와일드카드 '`?`'**를 지원합니다. 이는 API의 입출력 타입을 느슨하게 제한할 때 유용하며, PECS 원칙에 따라 다음과 같은 키워드를 사용할 수 있습니다. 
* Producer = `extends` (읽기 전용)
* Consumer = `super` (쓰기 전용)
```java
public class DtoPrinter {
  // 읽기 전용 API
  public void printDTOs(List<? extends BaseDTO> dtos) {
    for (BaseDTO dto : dtos) {
      System.out.println(dto.getId());
    }
  }
}
// List<UserDto> userDtos = List.of( .. );
// printDTOs(userDtos);


public EventHandlerRegisterer {
  // 쓰기 전용 API
  public void registerHandler(List<? super EventHandler> handlers, EventHandler handler) {
    handlers.add(handler);
  }
}
// allHandlers.add(new ClickHandler());
```

## 내가 이해한 바
### 제네릭과 함께 등장한 개념
자바의 제네릭은 객체 타입(참조 타입, reference type)만을 다룰 수 있기 때문에, 기본 타입(primitive type)은 직접 사용할 수 없습니다.
이를 해결하기 위해 int, double과 같은 기본 타입을 포장한 래퍼 클래스(wrapper class)들이 사용되며, 이 과정에서 자연스럽게 오토박싱(auto-boxing) 개념이 등장하게 되었습니다.

즉, 제네릭이 도입되면서 기본 타입을 자동으로 객체 타입으로 변환하거나, 그 반대로 변환하는 오토박싱과 언박싱이 함께 발전하게 된 것입니다. 
## 내가 개발할 떄 신경쓰는 점
## 개선된 점
