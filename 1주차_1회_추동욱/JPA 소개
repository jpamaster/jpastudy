# JPA 소개

# 1. JPA

- `Java Persistence API` , 자바의 `ORM` 기술 표준
- `JPA`를 사용함으로써, 이전과 달리 `SQL`을 직접 작성하지 않아도 되며, 개발 생산성 및 유지 보수 측면에서 좋다.
- `JPA`는 인터페이스의 모음이며, `JPA 2.1` 표준 명세를 구현한 3가지 구현체가 있다.
    - `Hibernate`, `EclipseLink`, `DataNucleus`
    

## 1.1 JPA의 사용 이유

- `SQL` 중심적인  개발에서 객체 중심으로 개발할 수 있다.
- JPA에 객체를 전달만 하면 되므로 SQL를 작성하고 JDBC API를 사용하는 반복적인 일을 JPA가 대신 처리해주어 생산성이 향상된다.
- 필드를 추가하거나 삭제되었다 하더라도 JPA가 일련의 과정을 대신 처리해주므로 수정해야할 코드가 SQL을 직접 다룰때보다 현저히 줄어든다.
- 상속, 연관 관계, 객체 그래프 탐색 등의 패러다임의 불일치 문제를 해결해준다.
- JPA는 애플리케이션과 데이터베이스 사이에 다양한 성능최적화 기능을 제공한다.
- 관계형 데이터베이스는 같은 기능도 벤더마다 사용법이 다른 경우가 많다. JPA는 애플리케이션과 데이터베이스 사이에 추상화된 데이터 접근 계층을 제공하여 애플리케이션이 특정 데이터베이스 기술에 종속되지 않도록 도와준다.

### 1.1.1 생산성

- 저장 : `jpa.persist(member)`
- 조회 : `Member member = jpa.find(memberId)`
- 수정 : `member.setName("name")`
- 삭제 : `jpa.remove(member)`

### 1.1.2. 유지보수

- `SQL`의 경우 기존 필드의 변경 시 모든 `SQL`을 수정해야 하는 반면에, `JPA` 는 간단하게 수정할 수 있다.

### 1.1.3 패러다임의 불일치를 해결할 수 있다.

1. 상속
    
    
    - 저장 : `Item` 과 `Album` 테이블에 각각 저장해야 한다.
    
    - 조회 : `Album` 을 조회할 경우, 각각의 테이블에 따른 조인 `SQL` 을 작성하고, 객체를 생성하는 등 복잡하다.
2. 연관관계
    
    
3. 객체 그래프 탐색
    - 처음 사용하는 SQL에 따라 탐색 범위 결정
    - 엔티티 신뢰성 문제
        
        ```java
        Member member = memberDao.find(memberId);
        member.getTeam(); //???
        member.getOrder().getDelivery(); //???
        ```
        
        ```java
        Member member = jpa.find(Member.class, memberId);
        Team team = member.getTeam(); //자유로운 객체 그래프 탐색
        ```
        
    - 모든 객체를 미리 로딩 할 수는 없다.
        
        <aside>
        💡 계층형 아키텍쳐 
        진정한 의미의 계층 분할이 어렵다
        
        </aside>
        
4. 비교하기
    
    ```java
    String memberId = "100";
    Member member1 = memberDao.get(memberId);
    Member member2 = memberDao.get(memberId);
    
    member1 == member2;  // 다르다.
    ```
    

- 동일한 트랜잭션에서 조회한 엔티티는 같음을 보장
    
    ```java
    String memberId = "100";
    Member member1 = jpa.find(Member.class, memberId);
    Member member2 = jpa.find(Member.class, memberId);
    
    member1 == member2;  // 같다
    ```
    

### 1.4 JPA의 성능 최적화 기능

- 1.1 1차 캐시와 동일성(identity) 보장
    - 같은 트랜잭션 안에서 같은 엔티티를 반환 - 약간의 조회 성능 향상
    - DB Isolation Level이 Read Commit이어도 애플리케이션에서 Repeatable Read 보장
        
        ```java
        String memberId = "100";
        
        // SQL 한 번만 실행
        Member m1 = jpa.find(Member.class, memberId); // SQL
        Member m2 = jpa.find(Member.class, memberId); // 캐시
        
        println(m1 == m2) // true
        ```
        
- 트랜잭션을 지원하는 쓰기 지연(transactional write-behind) - INSERT
    - 트랜잭션을 커밋할 때까지 INSERT SQL을 모음
    - JDBC BATCH SQL 기능을 사용해서 한번에 SQL 전송
        
        ```java
        transaction.begin(); // [트랜잭션] 시작
        
        em.persist(memberA);
        em.persist(memberB);
        em.persist(memberC);
        //여기까지 INSERT SQL을 데이터베이스에 보내지 않는다.
        
        //커밋하는 순간 데이터베이스에 INSERT SQL을 모아서 보낸다.
        transaction.commit(); // [트랜잭션] 커밋
        ```
        
- 지연 로딩(Lazy Loading)과 즉시 로딩
    - 지연 로딩 : 객체가 실제 사용될 때 로딩
        - 값이 실제로 필요한 시점에 JPA가 쿼리를 날린다.
    - 즉시 로딩 : JOIN SQL로 한번에 연관된 객체까지 미리 조회
        - Member를 사용할 때 대부분 Team도 같이 필요하다면 즉시 로딩이 좋다.
    - 옵션 변경 가능 : 애플리케이션 개발할 때는 지연 로딩으로 개발한 후에, 성능 최적화가 필요한 곳에만 즉시 로딩을 사용해 최적화하는 것을 추천
