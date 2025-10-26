# Two Sum [#1](https://leetcode.com/problems/two-sum/)

## 전략
배열에서 두 수의 합이 target되는 인덱스 쌍을 찾기 위해 다음 두가지 방법이 존재합니다. 
### 1. 브루트포스: 모든 쌍을 탐색한다
```java
for (int i = 0; i < nums.length; i++) {
 for (int j = i+1; j < nums.length; j++) {
   if (nums[i] + nums[j] == target) {
     return new int[]{i, j};
   }
 }
}
```
* 시간복잡도:
  * 외부 루프: `i`가 `0 ~ n-1`까지 반복하여, n번 반복
  * 내부 루프: `j`가 `i+1 ~ n-1`까지 반복하여, 평균적으로 `n/2`번 반복
  * 따라서, `1 + 2 + ... + (n-1) = n(n-1)/2`번 if문에서 비교하므로, 시간복잡도는 `O(n^2)`
* 공간복잡도:
  * 추가로 사용하느 ㄴ배열이나 자료구조가 없기 때문에, `O(1)`
### 2. 해시맵: `target - num` 존재 여부를 키로 빠르게 탐색
```java
Map<Integer, Integer> map = new HashMap<>();
for (int i = 0; i < nums.length; i++){
 map.put(nums[i], i);
}

for (int i = 0; i < nums.length; i++) {
  int complement = target - nums[i];
  if (map.containsKey(complement) && i != map.get(complement){
    return new int[]{i, map.get(complement)};
 }
}
```
* 시간 복잡도
  * 배열을 한 번 순회하므로, `n`번 반복
  * 각 반복에서,
    * `target - nums[i]` 계산은 `O(1)`
    * `map.containsKey(complement)`에서, HashMap 조회는 평균 `O(1)`
  * 따라서, `O(n)`
* 공간 복잡도
  * HashMap을 사용하여, 최대 n개의 key-value 쌍을 저장하므로, `O(n)`
  * 반환되는 `int[2]`는 상수크기이므로, `O(1)`
  * 따라서, `O(n) + O(1)`이므로 `O(n)`이다. 
* 코드 품질
  * `map`의 저장과 조회를 하나의 for문으로 처리할 수 있습니다.  
    ```java
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        if (map.containsKey(complement)) {
            return new int[]{map.get(complement), i};
        }
        map.put(nums[i], i);
    }
    ```



# 3Sum [#15](https://leetcode.com/problems/3sum/description/)

## 전략
### 1. 


