## join 정의

> 두 개 이상의 테이블들에 있는 데이터를 한 번에 조회하는 것


## join 표현 구문

### 1. 암시적 조인 ( implicit join )

> from절에는 table들만 나열하고 where절에 join condition을 명시하는 방식

✔️ 오래된 방식

✔️ selection condition과 join condition이 같이 있기 때문에 가독성이 떨어짐

✔️ 복잡한 join 쿼리를 작성시에는 실수 할 가능성이 높다.

```sql
# 예시 (ID가 1인 임직원이 속한 부서 이름 조회)
# selection condtion = E.id = 1
# join condition = E.dept_id = D.id
SELECT D.name FROM employee AS E, department AS D WHERE E.id = 1 and E.dept_id = D.id;
```

### 2. 명시적 조인 ( explicit join )


> from절에 JOIN 키워드와 함께 joined table들을 명시하는 방식


✔️ from절에서 ON 뒤에 join condition(E.dept_id = D.id)이 명시된다.

✔️ 가독성이 좋다

✔️ 복잡한 join 쿼리를 작성시에는 실수 할 가능성이 적다.

```sql
# 예시 (ID가 1인 임직원이 속한 부서 이름 조회)
SELECT D.name FROM employee AS E JOIN department AS D ON E.dept_id = D.id WHERE E.id = 1;
```

## join의 종류

### 1. 내부 조인 ( inner join )


> 두 테이블에서 join condition을 만족하는 tuple들로 result table을 만드는 join


✔️ join condition에 사용 가능한 연산자 : =, <, >, ≠ 등등 여러 비교 연산자 사용 가능

✔️ join condtion에서 null 값을 가지는 tuple은 result table에 포함되지 못한다.

```sql
# INNER는 생략 가능
# FROM table1 [INNER] JOIN table2 ON join_condition
SELECT * FROM employee AS E [INNER] JOIN department AS D ON E.dept_id = D.id;
```

- 내부 조인 동작 예시
    - 사원 부서 테이블
      ![](https://velog.velcdn.com/images/zvyg1023/post/4c6f2889-9986-4e55-a1a2-84e59753bc6e/image.png)



    - 내부 조인 결과
        
        ![](https://velog.velcdn.com/images/zvyg1023/post/3dc1d309-54f2-4f0e-a3c6-0715b81a3bcc/image.png)



### 2. 외부 조인 ( outer join )


> 두 테이블에서 join condition을 만족하지 않는 tuple들도 result table에 포함하는 join


✔️ join condition에 사용 가능한 연산자 : =, <, >, ≠ 등등 여러 비교 연산자 사용 가능

```sql
# OUTER는 생략 가능
# FROM table1 LEFT [OUTER] JOIN table2 ON join_condition
# FROM table1 RIGHT [OUTER] JOIN table2 ON join_condition
# FROM table1 FULL [OUTER] JOIN table2 ON join_condition
SELECT * FROM employee AS E LEFT [OUTER] JOIN department AS D ON E.dept_id = D.id;
```

- 외부 조인 동작 예시
    - 사원, 부서 테이블
      ![](https://velog.velcdn.com/images/zvyg1023/post/cb8a6b58-19ad-40f0-be85-e93b9d531148/image.png)



    - 왼쪽 외부 조인 결과
        ![](https://velog.velcdn.com/images/zvyg1023/post/d41cf3a3-c1f8-49c9-85e3-d9da97051955/image.png)

        
        
    - 오른쪽 외부 조인 결과
        ![](https://velog.velcdn.com/images/zvyg1023/post/c05ee1e0-b4b2-45ab-a1a4-fda1dbb90936/image.png)

        
        
    - 완전 외부 조인 결과
        
        ⚠️ MYSQL은 FULL OUTER JOIN을 지원하지 않음
        
        ![](https://velog.velcdn.com/images/zvyg1023/post/67bdd8d5-dbba-4e60-b6d8-5348cf144766/image.png)




### 3. 등가 조인 ( equi join )


> join condition에서 =를 사용하는 join


✔️ 내부 조인, 외부 조인도 등가 조인이 될 수 있다.

### 4. USING 조건절

✔️ 두 테이블이 등가 조인(equi join)할 때 join하는 attribute의 이름이 같다면, USING으로 간단하게 작성

✔️ 이 떄 같은 이름의 attribute는 result table에 한번만 표시 된다.

✔️ 내부 조인, 외부 조인 둘 다 사용 가능하다.

```sql
SELECT * FROM employee E INNER JOIN department D USING(dept_id);
```

- USING 동작 예시

  ![](https://velog.velcdn.com/images/zvyg1023/post/d05c94b9-9729-432b-90ad-b26a835a48c4/image.png)



### 5. 자연 조인 ( natural join )

> 두 테이블에서 같은 이름을 가지는 모든 attribute pair에 대해서 equi join을 수행


✔️ join condition을 따로 명시하지 않는다.

```sql
# 자연 내부 조인 : FROM table1 NATURAL [INNER] JOIN table2
SELECT * FROM employee E NATURAL INNER JOIN department D;

# 자연 외부 조인 : FROM table1 NATURAL LEFT [OUTER] JOIN table2
SELECT * FROM employee E NATURAL LEFT OUTER JOIN department D;
```

- 자연 조인 동작 예시
    - 자연 내부 조인 결과
      ![](https://velog.velcdn.com/images/zvyg1023/post/beeb5fe1-86b9-49af-84d6-fcf4790617ae/image.png)

      ![](https://velog.velcdn.com/images/zvyg1023/post/102af1e1-4d7e-4499-beee-55dfccf0b427/image.png)




### 6. 교차 조인 ( cross join )


> 두 테이블의 tuple pair로 만들 수 있는 모든 조합을 result table로 반환



✔️ join condition이 없다.

```sql
# 암묵적 조인 : FROM table1, table2
SELECT * FROM employee, department;

# 명시적 조인 : FROM table1 CROSS JOIN table2
SELECT * FROM employee CROSS JOIN department;
```

- 교차 조인 동작 예시

  ![](https://velog.velcdn.com/images/zvyg1023/post/65ccf617-0eca-408a-ae01-a6cb6f2a9c5e/image.png)



### 7. 셀프 조인 ( self join )


> 테이블이 자기 자신에게 join하는 경우



✔️ 내부 조인, 외부 조인 둘 다 사용 가능하다.

```sql
SELECT * FROM table t1 JOIN table2 ON t1.column1 = t2.column2; 
```

---
## 참고
[SQL로 데이터 조회하기! join의 의미와 여러 종류의 join들을 쉽게 정리해서 설명합니다! join을 사용한 예제도 있으니까요, 쉬운 설명 들어보세용~ :)](https://www.youtube.com/watch?v=E-khvKjjVv4)