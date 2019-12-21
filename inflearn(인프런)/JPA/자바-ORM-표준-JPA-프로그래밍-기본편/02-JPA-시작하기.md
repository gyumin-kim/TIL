# 02 JPA 시작하기

## persistence.xml
- JPA 설정 정보
- `META-INF/persistence.xml`에 위치해야 한다.
![image](https://user-images.githubusercontent.com/26949964/71306581-88754c80-2425-11ea-8fe9-0e188fd58de0.png)
1. `Persistence` 객체가 `persistence.xml`의 설정 정보를 조회한다.
2. `Persistence` 객체가 `EntityManagerFactory`를 만든다.
3. `EntityManagerFactory`로부터 `EntityManager`들이 생성된다.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<persistence xmlns="http://xmlns.jcp.org/xml/ns/persistence"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" version="2.2"
             xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_2.xsd">
    
    <persistence-unit name="hello">
        <properties>
            <!-- 필수 속성 -->
            <property name="javax.persistence.jdbc.driver" value="org.h2.Driver"/>
            <property name="javax.persistence.jdbc.user" value="sa"/>
            <property name="javax.persistence.jdbc.password" value=""/>
            <property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test"/>
            <property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"/>

            <!-- 옵션 -->
            <!-- hibernate가 실행한 SQL을 출력 -->
            <property name="hibernate.show_sql" value="true"/>
            <!-- SQL을 출력할 때 보기 쉽게 정렬 -->
            <property name="hibernate.format_sql" value="true"/>
            <!-- query 출력 시 주석도 함께 출력 -->
            <property name="hibernate.use_sql_comments" value="true"/>
            <!-- JPA 표준에 맞춘 새로운 키 생성 전략을 사용 -->
            <!--<property name="hibernate.id.new_generator_mappings" value="true"/> -->
        </properties>
    </persistence-unit>
</persistence>
```

---
### persistence unit
- (일반적으로) 연결할 데이터베이스당 하나의 영속성 유닛(persistence unit)을 등록한다.

---
### Dialect
- JPA는 특정 데이터베이스에 종속적이지 않은 기술이다.
- 개발자는 JPA의 표준 문법에 맞춰 개발하면 되고, 설정한 dlalect에 맞게 특정 데이터베이스에 의존적인 SQL을 생성한다. 따라서 데이터베이스가 변경되어도 전체 애플리케이션 코드들을 변경할 필요 없이, dialect 설정만 바꿔주면 된다.


## Hello JPA - 애플리케이션 개발

### EntityManager 설정
- JPA를 시작하기 위해서는, `persistence.xml`의 설정 정보를 사용해서 EntityManagerFactory를 생성해야 한다.
  - 이 때 `Persistence` 클래스를 사용한다.
  ```java
  EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");
  ```
  - `META-INF/persistence.xml`에서 이름이 "hello"인 persistence-unit(영속성 유닛)을 찾아서 **Entity Manager Factory**를 생성한다.
    - JPA를 동작시키기 위한 기반 객체를 생성하고, (JPA 구현체에 따라서는) DB Connection Pool도 생성하므로, 비용이 매우 큰 작업이다.
    - 따라서 **애플리케이션 전체에서 딱 1번만 생성**하고, 공유해서 생성해야 한다.
- EntityManagerFactory로부터 EntityManager를 생성한다.
  - JPA의 대부분의 기능을 EntityManager가 제공한다(CRUD).
  - 개발자 입장에서는 EntityManager를 가상의 데이터베이스로 생각할 수 있다(EntityManager는 내부에 data source(connection)을 유지하면서 데이터베이스와 통신함).
  - EntityManager는 DB Connection과 밀접한 관련이 있으므로, thread 간에 절대 공유하면 안된다(사용하고 버려야 한다).
  - **JPA의 모든 데이터 변경은 transaction 안에서 실행해야 한다.**
- 사용이 끝난 EntityManager와 EntityManagerFactory는 아래와 같이 반드시 종료해야 한다.
  ```java
  em.close();
  emf.close();
  ```

### transaction 관리
- 항상 transaction 안에서 데이터를 변경해야 하며, transaction 없이 데이터를 변경하면 exception이 발생한다.
- transaction은 아래와 같이 받아온다.
  ```java
  EntityTransaction tx = em.getTransaction();
  try {
    tx.begin();     // transaction 시작
    // 비즈니스 로직 수행 ...
    tx.commit();    // transaction commit
  } catch (Exception e) {
    tx.rollback();  // exception 발생 시 transaction rollback
  }
  ```


### 비즈니스 로직
#### 등록
```java
String id = "id1";
Member member = new Member();
member.setId(id);
member.setUsername("gyumin");
member.setAge(27);

em.persist(member); // 등록 (entity 저장)
```

JPA는 'Member' entity의 매핑 정보(annotation)을 분석해서 다음의 SQL을 만들어 DB에 전달한다.
```sql
INSERT INTO MEMBER (ID, NAME, AGE) VALUES ('id1', 'gyumin', 27);
```

#### 수정
```java
member.setAge(28);
```
- JPA를 통해서 entity 객체를 가져오면 그 객체를 JPA가 관리(추적)한다.
  - 해당 entity 객체의 값을 바꾼 경우, 달라진 부분을 commit 시점에 JPA가 체크한 뒤 UPDATE query를 수행한다.
  - 따라서 `em.update()` 같은 메서드는 존재하지 않는다.
```sql
UPDATE MEMBER
  SET AGE = 28
WHERE ID = 'id1'
```

#### 삭제
```java
em.remove(member);
```
```sql
DELETE FROM MEMBER WHERE ID = 'id1';
```

#### 한 건 조회
```java
Member findMember = em.find(Member.class, id);
```
find()의 parameter로 1) entity 타입과 2) entity 객체에서 `@Id`를 사용하여 데이터베이스 테이블의 기본 키와 매핑한 식별자 값을 넘겨서, entity 하나를 조회한다.
```sql
SELECT * FROM MEMBER WHERE ID = 'id1';
```


### JPQL
- JPA를 사용하면, **애플리케이션 개발자는 entity 객체를 중심으로 개발**하고, DB에 대한 처리는 JPA에 맡겨야 한다.
- 검색 query의 경우에도 ~~테이블~~이 아닌 **entity 객체를 대상으로 검색**해야 한다.
  - 그런데 DB의 모든 데이터를 불러온 다음 entity 객체로 변경한 후에 검색해야 하는데, 이는 사실상 불가능하다.
  - 필요한 데이터만 DB로부터 불러오려면, 결국 **검색 조건이 포함된** SQL을 사용해야 한다.
  - 이를 해결하는 것이 JPQL(Java Persistence Query Language)이다.
- JPQL은 **SQL을 추상화한 객체지향 쿼리 언어**다.
  - JPQL은 entity 객체를 대상으로 query한다. 즉, 클래스와 필드를 대상으로 query한다.  
    (SQL은 DB table을 대상으로 query)
- `select m from Member m` 에서의 Member는 entity 객체를 뜻한다(MEMBER 테이블 아님).
  - **JPQL은 DB table을 전혀 알지 못한다**.