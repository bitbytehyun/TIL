# Stream
## 실제 개념
스트림은 자바 8부터 도입된 효율적인 데이터 처리 API입니다. 
컬렉션(또는 배열 등) 내부의 데이터를 연속된 흐름으로 추상화하여, 마치 데이터 파이프라인처럼 처리할 수 있도록 도와줍니다.   
스트림은 실제 데이터를 저장하지 않고, **데이터 처리에 필요한 연산만을 정의**합니다. 

```java
// stream 사용 전
List<String> result = new ArrayList<>();
for (String s : list) {
  if (s.startWith("A")) {
    result.add(s.toUpperCase());
  }
}
// stream 사용 후
List<String> result = list.stream()
  .filter(s -> s.startWith("A"))
  .map(String::toUpperCase)
  .collect(Collectors.toList());
```
### 특징
* 람다 표현식에 기반합니다.
* 지연 연산으로, 중간 연산은 바로 실행되지 않고 최종 연산 시에만 실행됩니다. 이때, 원본 데이터를 변경하지 않습니다.
* `.parallelStream()`으로 병렬 처리가 가능합니다.

### 스트림 연산
스트림은 다음 3단계로 처리됩니다. 
1. 생성: 컬렉션, 배열, 무한 스트림, 특정 값으로 스트림을 생성할 수 있습니다.
1. 중간 연산 : 스트림을 다른 스트림으로 변환합니다. 지연 실행으로 명령어 실행 시 바로 작동하지 않고 연산 정보를 내부에 등록한 뒤, 최종 연산에서 한번에 실행됩니다.
1. 최종 연산 : 중간연산으로 만든 파이프라인을 실제로 실행하여, 결과를 반환합니다. 이 때, 한 요소기 스트림 전체를 거치며 처리됩니다.

### 스트림의 동작을 구현
스트림이 처음 접했을 땐 파이프라인과 지연 연산 개념이 잘 들어오지 않을 수 있기 때문에 직접 구현해보았습니다. 
해당 코드를 통해, 스트림이란 함수 목록을 쌓아두는 것이라는 감을 잡을 수 있었습니다.
```java
public class MyStream<T> {
    private List<T> source;
    private List<Function<Object, Object>> operations = new ArrayList<>();

    public MyStream(List<T> source) {
        this.source = source;
    }

    public static <T> MyStream<T> of(List<T> source) {
        return new MyStream<>(source);
    }

    public <R> MyStream<R> map(Function<? super T, ? extends R> mapper) {
        operations.add(obj -> mapper.apply((T) obj));
        return (MyStream<R>) this;
    }

    public MyStream<T> filter(Predicate<? super T> predicate) {
        operations.add(obj -> predicate.test((T) obj) ? obj : null);
        return this;
    }

    public List<T> collect() {
        List<T> result = new ArrayList<>();
        for (T item : source) {
            Object current = item;
            for (Function<Object, Object> op : operations) {
                current = op.apply(current);
                if (current == null) break;
            }
            if (current != null) result.add((T) current);
        }
        return result;
    }
}

// 사용 예시
public static void main(String[] args) {
    List<String> data = Arrays.asList("apple", "banana", "orange");
    MyStream.of(data)
            .filter(s -> s.startsWith("a"))
            .map(s -> s.toUpperCase())
            .collect();
}
```


## 내가 이해한 바
## 내가 개발할 때 신경쓰는 점
## 개선된 점
