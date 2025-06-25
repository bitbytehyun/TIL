# 컬렉션과 리스트
## 실제 개념
컬렉션 프레임워크는 자바 1부터 존재해온 **데이터를 효율적으로 저장하고 조작**하기 위한 자료구조와 알고리즘의 집합입니다.    
자바에서 컬렉션은 일관된 인터페이스와 다양한 구현체로, 다양한 데이터 구조를 손쉽게 사용할 수 있도록 도와줍니다. 
* 컬렉션 프레임워크의 **최상위 인터페이스**는 `java.util.Collection<E>`입니다. 이 인터페이스는 `List`, `Set`, `Queue`의 공통 부모이며, 각각의 자료구조가 제공하는 기능을 표준화합니다.
* 단, `Map`은 **key-value 쌍을 저장**하기 때문에 `collection`을 상속받지 않습니다. `Map`은 컬렉션 프레임워크에 포함되지만, 별도의 계층 구조로 분리되어 있습니다.

### List
`List`는 순서가 있는 데이터 구조로, 중복을 허용합니다. 
배열과 유사하지만, 동적 크기 조정 및 다양한 편의 매서드(`add`, `remove`, `contains` 등)을 제공합니다. 대표적인 구현체로는 `ArrayList`와 `LinkedList`가 있습니다.    
### ArrayList
* `ArrayList`는 내부적으로 `Object[] element` 배열과 `int size` 필드를 갖고 있으며, 이를 힙 메모리에 저장합니다. `ArrayList`의 참조 변수는 스택에 저장됩니다.
  ```java
  public class ArrayList<E> extends AbstractList<E> {
    transient Object[] elementData;
    private int size;
  }
  ```
* 생성 시 지정한 초기 용량 (capacity) 만큼 배열이 할당되고, 그 이상 추가되면 `grow()` 매서드가 호출되어, 기존 배열보다 1.5배 또는 2배 큰 새 배열을 생성한 뒤, 기존 데이터를 `System.arraycopy()`로 복사합니다. 기존 배열은 더 이상 참조되지 않아, GC의 대상이 됩니다.

### LinkedList
* `LinkedList`는 배열 기반이 아닌 이중 연결 리스트 기반입니다. 내부적으로 `Node<E>`라는 정적 내부 클래스를 사용하며, 각 노드는 다음과 같은 필드를 가집니다.
  ```java
  private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;
  }
  ```
* `LinkedList` 자체는 `first`, `next` 노드에 대한 참조만 갖습니다.
  ```java
  public class LinkedList<E> extends AbstractSequentialList<E> {
    transient Node<E> first;
    transient Node<E> last;
    private int size = 0;
  }
  ```
* 새로운 노드가 추가될 때, 새 `Node` 객체는 힙에 생성되며, 기존 노드의 `next`, 새 노드의 `prev`를 연결하여 리스트를 구성합니다. 각 노드는 독립된 객체로, 특정 노드만 GC로 수거되는 것이 가능합니다.
* `LinkedList`는 `Deque` 인터페이스를 구현하고 있기 때문에, **FIFO(First-In-First-Out)** 원칙을 따르는 Queue 구조로도 활용가능합니다. 이중 연결리스트로 구현된 `LinkedList` 특성상, 맨 앞과 맨 뒤의 삽입/삭제가 O(1) 시간 복잡도로 매우 빠릅니다. `offer(E e)`, `poll()`, `peek()` 매서드를 사용할 수 있습니다.  
# 내가 이해한 바
# 내가 개발할 때 신경쓰는 점
# 개선된 점
