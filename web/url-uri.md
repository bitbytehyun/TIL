# URL, URI란
## 실제 개념
### URI (Uniform Resource Identifier)
* 자원을 식별하는 통합 규약입니다.
* 인터넷에서 어떤 자원을 가리키는 식별자로, URL과 URN으로 나뉩니다.

### URL (Uniform Resource Locator)
* 자원이 어디있는지 위치를 알려주는 식별자입니다.
* `스킴://호스트:포트/경로?쿼리#프레그먼트` 구조를 가지고 있습니다.

```bash
https://example.com:443/users/123?active=true#section1
```
    * `https`: 스킴 (프로토콜)
    * `example.com:443`: 호스트 + 포트
    * `/users/123`: 경로
    * `?active=true`: 쿼리
    * `#section1`: 프레크먼트 (문서 내 위치)

### URN (Uniform Resource Name)
* 자원의 이름으로만 식별하는 방식으로, 위치와 무관합니다.
* 현재는 거의 쓰이지 않습니다.
* 예: 책 ISBN을 나타내는 URN인 `urn:isbn:19493944`
