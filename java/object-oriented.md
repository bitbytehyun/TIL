# 객체지향이란
## 실제 개념

### 객체지향의 출발
객체지향은 "변경의 영향 최소화"에서 출발했습니다. 
과거 절차지향 개발은 의존성과 결합도가 높아, 하나를 바꾸면 앞뒤 코드 전체를 다시 테스트해해야 했습니다. (실제로 제가 담당하고 있는 레거시 Node.js 앱은 API 단위로만 테스트가 가능해서, 작은 수정에도 전체 플로우를 다시 점검해야 하는 어려움이 있습니다.)

객체지향은 이런 문제를 줄이기 위해 등장했습니다.
자동차에서 타이어를 교체해도 엔진과 핸들이 그대로 동작하는 것처럼, **인터페이스만 맞으면 구현체를 갈아끼워도 시스템은 정상 동작**해야 합니다.

따라서, 객체지향의 핵심 가치는 **실수를 줄이고, 유지보수를 안전하게 하며, 코드를 교체하기 쉽게 만드는 것**입니다.


### 객체와 네 가지 특성
객체지향은 행동과 상태를 가진 객체들로 소프트웨어를 모델링합니다.

예를 들어, "스터디카페 좌석 예약"을 절차적으로 구현하면 다음과 같은 한 덩어리 흐름이 나옵니다.  
1. 사용자 ID 확인
2. 좌석 유효성 확인
3. 결제 여부 확인
4. 이용권 발급 

하지만, 객체지향적으로 접근하면 다음 객체들이 서로 협력하면서 예약을 완성합니다.
* `User` 객체
* `Desk` 객체
* `Payment` 객체
* `Reservation` 객체

이 객체들의 대원칙은 결합도는 출이고, 응집도는 낮추자는 것입니다. 
이를 통해 각 객체가 자신의 역할에 집중할 수 있고, 시스템 전체가 유연해집니다.

이러한 구조를 가능하게 하는 네가지 특성이 바로 `캡창추다`로 많이 알고 있는 **캡슐화, 상속, 추상화, 다형성**입니다.
* **캡술화**: 내부 상태를 감추고 필요한 것만 공개
* **상속**: 부모의 상태와 행동을 자식이 물려줌
* **추상화**: 공통된 행동을 패턴화해 여러 구현체로 확장 가능하게 함
* **다형성**: 같은 행동이 상황과 구현에 따라 다르게 실행됨

다만 상속은 결합도가 100% 만들어 변경에 취약해지는 경우가 많습니다. 
그래서 최근에는 **상속보다는 합성**을 선호합니다.

### SOLID 원칙
객체지향 설계를 더 정교화한 것이 **SOLID** 원칙입니다.
* **단일 책임 원칙 (SRP)**: 한 클래스는 하나의 책임만 가져야 한다. 
* **개방-폐쇄 원칙 (OCP)**: 확장에는 열려 있고, 변경에는 닫혀 있어야 한다.
* **리스코프 치환 원칙 (LSP)**: 부모 타입을 자식 타입으로 안전하게 대체할 수 있어야 한다.
* **인터페이스 분리 원칙 (ISP)**: 불필요하게 큰 인터페이스 대신 세분화된 인터페이스를 사용한다.
* **의존 역전 원칙 (DIP)**: 구현체가 아닌 인터페이스에 의존해야 한다. 

예를 들어, 결제 방식을 처리한다고 할 때, 아래처럼 DIP를 적용할 수 있습니다. 
```kotlin
interface PaymentProcessor {
    fun pay(amount: Money): PaymentResult
}

class KakaoProcessor : PaymentProcessor {
    override fun pay(amount: Money) = PaymentResult.success("kakao")
}
class TossPayProcessor : PaymentProcessor {
    override fun pay(amount: Money) = PaymentResult.success("toss")
}

class ReservationService(
    private val paymentProcessor: PaymentProcessor // 구현체가 아닌 인터페이스에만 의존하여, 결제 방식에 따른 변경의 영향을 최소화
) {
    fun reserve(userId: Long, deskId: Long, hours: Int): Reservation {
        val amount = Money.of(1000L * hours)
        val result = paymentProcessor.pay(amount)
        if (result.success) {
            return Reservation(userId, deskId, hours)
        }
        throw IllegalStateException("payment failed")
    }
}
```
더 나아가 ISP는 인터페이스를 공용으로 쓰면 변경 영향이 커지기 때문에 여러개로 세분화하여 분리하고자 하는 바람입니다. 

### 디자인 패턴과 객체지향
디자인 패턴은 객체지향 원칙을 실무 코드에 효과적으로 적용하는 방법입니다 
* 전략 패턴: 결제 방식을 런타임에 교체 가능하게 하여 OCP 실현 
* 팩토리 패턴: 객체 생성을 분리하여 DIP를 실현
* 템플릿 매서드 패턴: 공통된 프로세스에서 일부 단계만 하위 클래스가 정의 

### DDD와 아키텍처
디자인 패턴이 클래스/매서드 단위의 설계 기법이라면, **DDD (Domain Driven Design)** 와 **헥사고날 아키텍처**는 시스템 전체 구조를 객체지향적으로 만드는 방법입니다. 
* DDD
  * `Entity`: Reservation, User, Desk 같이 식별 가능한 객체
  * `Value Object`:Money, Period 같은 불변 값 객체
  * `Aggregate`: Reservation(연장/환불 규칙 포함)
  * `Repository`: ReservationRepository (DB 추상화)
* 헥사고날 아키택처
  * Ports: ReservationService, PaymentProcessor
  * Adapters: KakaoPayProcessor, JpaReservationRepository
  * 경계를 명확하게 하여, 외부 구현을 쉽게 교체 가능

### 테스트 전략
객체지향의 목적은 유지보수성을 높이는 것인데, 이것은 결국 테스트하기 쉬운 코드로 이어집니다. 
* 도메인 단위 테스트: Reservation.extend(), Reservation.cancel() 같은 규칙을 DB 없이 검증 
* 캐릭터라이제이션 테스트: 레거시 Node.js 동작을 Kotlin 테스트로 고정
* 인수 테스트: "예약 -> 연장 -> 환불" 핵심 플로우를 시나리오로 검증
* 계약 테스트: v1(node) -> v1(kotlin) API의 응답 일관성 보장

### 정리
코드는 한달만이라도 지나면 금새 레거시가 되고, 내가 짠 코드도 금방 낯설어집니다. 
그렇기 때문에 고치기 쉬운 코드가 곧 좋은 코드입니다. 

객체지향은 이를 실현하기 위한 사고방식이자, 약속이며, 개발자가 지켜야 할 태도입니다. 
* 원칙: 캡상추다, SOLID
* 구현: 디자인패턴
* 시스템: ddd, 헥사고날 아키택처
* 검증: 테스트 전략

이 과정을 반복하며 학습하고 적용한다면, 코드를 더 잘 짜고 유지보수가 쉬운 시스템을 만들어갈 수 있습니다. 
