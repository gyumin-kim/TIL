# Entity 매핑

## 객체와 테이블 매핑
### @Entity
- `@Entity`가 붙은 클래스는 JPA가 관리한다.
- JPA를 사용해서 **테이블과 매핑**할 클래스는 `@Entity` 필수
- 주의사항
  - **기본 생성자 필수**(public 또는 protected)
  - final 클래스, enum, interface, inner 클래스 사용 불가
  - 저장할 필드에 final 사용하면 안 된다.
- `@Entity`의 'name' 속성으로 JPA에서 사용할 entity 이름을 지정할 수 있다.
  - 기본값은 클래스 이름을 그대로 사용하며, 같은 클래스 이름이 따로 있는 게 아니라면 가급적 기본값을 그대로 사용한다.

### @Table
- Entity와 매핑할 테이블을 지정
- name 속성에는 데이터베이스의 **매핑할 실제 테이블 이름**을 적어준다.

---
## 데이터베이스 스키마 자동 생성
- 애플리케이션 실행 시점에 데이터베이스 테이블(DDL)을 자동으로 생성한다.
- ~~테이블 중심~~ → 객체 중심
- Entity 객체에 특정 필드가 추가되거나 삭제되더라도 DDL을 수정하고 새로 테이블을 생성하는 등의 작업을 할 필요가 없다.
- 데이터베이스 방언 설정에 따른 적절한 DDL 생성
- 이 기능을 사용해서 생성된 DDL을 봄으로써, Entity와 테이블이 어떻게 매핑되는지 쉽게 이해할 수 있다.
- `create`, `create-drop`, `update`, `validate`, `none`
- 주의사항
  - 이렇게 **생성된 DDL은 개발 장비에서만 사용**해야 한다. 운영 환경에서 사용할 만큼 완벽하지 않다.
  - 운영 장비에는 **절대 create, create-drop, update 사용하면 안된다**.
  - 개발 초기 단계는 create 또는 update 권장
  - 테스트 서버는 update 또는 validate 권장
  - 스테이징과 운영 서버는 validate 또는 none 권장

### DDL 생성 기능
- 예시
  ```java
  @Column(nullable = false, length = 10)
  ```
- DDL 생성 기능은 단지 DDL을 자동 생성할 때만 사용되고, JPA의 실행 로직에는 영향을 주지 않는다.
  - 즉 스키마 자동 생성 기능을 사용하지 않고 직접 DDL을 만든다면, DDL 생성 기능은 사용할 이유가 없다.

---
## 필드와 컬럼 매핑
![image](https://user-images.githubusercontent.com/26949964/71820926-09923c00-30d4-11ea-9593-6b880f61cc6b.png)

### @Column
![image](https://user-images.githubusercontent.com/26949964/71820956-23cc1a00-30d4-11ea-9585-b0c4b98750d0.png)
![image](https://user-images.githubusercontent.com/26949964/71820988-37778080-30d4-11ea-8d6d-f620d9355071.png)
- `unique` 속성의 경우, 제약조건 이름을 지정할 수 없어 잘 사용하지 않는다(임의의 이름이 생성됨). 대신 클래스 레벨에서 `@Table`의 `uniqueConstraints` 속성을 통해 제약조건 이름을 지정할 수 있다.

### @Enumerated
- `EnumType.ORDINAL` (default)
- `EnumType.STRING`
- `EnumType.ORDINAL`의 경우 enum에 element가 추가되거나 할 경우 값의 의미가 완전히 달라질 위험이 있으므로, 반드시 `@Enumerated(EnumType.STRING)`으로 명시하여 사용하도록 한다.

### @Temporal
(Java 8 이상의 LocalDate와 LocalDateTime을 사용할 경우, `@Temporal`은 사용하지 않아도 된다.)
![image](https://user-images.githubusercontent.com/26949964/71821153-af45ab00-30d4-11ea-9eb5-539b3debed89.png)

### @Lob
- CLOB: String, char[], java.sql.CLOB
- BLOB: byte[], java.sql.BLOB

### @Transient
- 이 필드는 매핑하지 않는다.
- 따라서 데이터베이스에 저장하지도, 조회하지도 않는다.
- 객체에 어떤 값을 (메모리에만) 임시로 보관하고 싶을 때 사용한다.

---
## 기본 키 매핑
