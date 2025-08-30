# inner join, outer join이란
## 실제 개념
JOIN이란 관계형 데이터베이스에서 두가지 이상의 테이블을 연결해서 데이터를 조회하는 방법입니다. 
JOIN은 크게 inner join, outer join으로 나눌 수 있고, 이를 이해하는 것은 집합 그림을 통해 쉽게 알 수 있습니다.
### INNER JOIN
두 테이블에서 조건을 만족하는 교집합 데이터만 반환합니다. JOIN이라 하면, 보통 INNER JOIN을 이야기합니다.
### OUTER JOIN
OUTER JOIN은 조건에 맞지 않는 데이터도 포함시켜서 반환하는 조회 방법입니다. 
1. LEFT OUTER JOIN
    * 왼쪽 테이블의 모든 행을 가져오고, 오른쪽 테이블에 값이 없다면 NULL로 채웁니다.
1. RIGHT OUTER JOIN
1. FULL OUTER JOIN
    * 양쪽 테이블의 모든 행을 가져와서, 서로 매칭되지 않는 부분은 모두 NULL로 채웁니다. 합집합을 의미합니다.
