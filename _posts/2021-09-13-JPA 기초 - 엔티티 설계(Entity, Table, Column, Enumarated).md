### Entity

`@Entity`가 붙은 클래스는 JPA에서 관리한다. JPA를 통해 DB 테이블과 매핑하려는 클래스에는 반드시 `@Entity` 어노테이션이 있어야 한다.

- Entity 이름을 클래스명이 아닌 다른 이름으로 설정하고 싶은 경우 `@Entity(name = "entityName")`으로 설정할 수 있다.
- Entity에는 기본 생성자가 필수로 필요하며, final, enum, interface, inner 클래스는 사용할 수 없다.

### Table

`@Table`을 통해 객체와 매핑할 테이블을 지정한다.

- name - 매핑할 테이블 이름을 지정한다.
- schema, catalog - schema, catalog 기능이 있는 DB에서 해당 schema, catalog를 매핑
- uniqueConstraints - 테이블의 유니크한 제약조건을 지정한다.

```java
import javax.persistence.*;

@Entity
@Table(
        name = "member",
        uniqueConstraints = @UniqueConstraint(
                columnNames = {"name", "age"}
        ) // 이름과 나이가 같은 객체는 삽입될 수 없음
)
public class User {

    @Id @GeneratedValue
    private Long id;
    private String name
;
		private int age;

```

### Column

`@Column` 을 통해 객체의 필드와 DB의 칼럼을 매핑한다.

- DB에 저장되는 칼럼명을 다른 이름으로 변경하고 싶은 경우에는 `@Column(name="column_name")`으로 설정한다.
- length - 칼럼의 최대 허용 길이, Default는 255
- insertable, updatable- 칼럼의 수정여부를 결정한다. Default는 True
- nullable - null값을 허용할 지 여부. Default는 False
- unique - 칼럼에 중복 값을 허용할 지 여부. Default는 False
- precsion, scale : BigInteger, BigDecimal 타입에서 사용한다. 각각 소수점 포함 자리수, 소수의 자리수를 의미함.

```java
import javax.persistence.*;

@Entity
public class User {

    @Id @GeneratedValue
    @Column(name = "member_id")
    private Long id;

    @Column(length = 10)
    private String name;
		@Column(insertable = True, nullable = True)
    private int age;
	
		@Column(unique = True, updatable = False)
		private string securityNumber

```

### Enumerated

`@Enumerated` 는 Enum 타입을 매핑할 때 사용한다. value로는 `EnumType.ORDINAL` 과 `EnumType.String`을 사용할 수 있다. 

그러나 운영에서는 `EnumType.Ordinal`만 사용하는 것을 권장한다. `Ordinal`은 DB에 값이 저장되는 반면 `String`을 사용할 경우,  값이 아니라 0과 1과 같이 숫자가 매칭되어 저장된다. 

예를 들어, 고객 타입을 Admin / User 인 Enum으로 매핑할 경우, String은 DB에 Admin으로 저장되지만, Ordinal은 0, 1로 저장된다. 

만약 나중에 VIP 타입이 추가되면서 원래는 Admin - 0,  User - 1 로 매핑되어 있던 값이 Admin - 0,  VIP - 1, User - 2로 변경되어 버리면 기존에 User로 매핑되어 있던 1이라는 값이 모두 VIP와 매핑되어버리는 심각한 문제가 발생할 수 있다. 

```java
import javax.persistence.*;

@Entity
public class User {

    @Id @GeneratedValue
    @Column(name = "member_id")
    private Long id;

    @Enumerated(EnumType.STRING)
    private Status status;
```

### Transient

`@Transient` 을 사용하면 해당 칼럼은 DB에 매핑되지 않는다. 객체에는 저장되지만 DB에는 저장되지 않으므로, 임시로 사용하려는 데이터에 주로 사용한다. 많이 사용되지는 않는다.

```java
import javax.persistence.*;

@Entity
public class User {

    @Id @GeneratedValue
    @Column(name = "member_id")
    private Long id;

		@Transient
		private String temp; // 해당 칼럼은 DB에 매핑되지 않음
```

### 출처

- [자바 ORM 표준 JPA 프로그래밍 - 기본편](https://www.inflearn.com/course/ORM-JPA-Basic)