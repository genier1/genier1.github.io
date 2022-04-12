개발을 하다보면 Repository에서 Model을 가져와서 DTO로 변환해 API 응답으로 내려주는 경우가 많다. 이럴 때, MapStruct 라이브러리를 사용하면 손쉽게 해결할 수 있다.

Mapstruct는 다른 매퍼 라이브러리에 비해 간편하며, 리플렉션 대신 method invocation을 사용하기 때문에 [성능도 좋아서](https://www.baeldung.com/java-performance-mapping-frameworks) 많은 곳에서 사용되고 있다. 생각보다 많은 기능들이 있어서, 이번 기회에 자주 사용하는 것들을 정리하고자 한다. 

### MapStruct 사용법 기초

- Order

```java
@Data
@Table(name = "orders")
@Entity
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String person;
    private int saleprice;
    private int originalPrice;
}
```

- OrderDTO

```java
@Data
public class OrderDto {
		private String person;
    private int originalPrice;
    private int saleprice;
}
```
우선 Entity와 DTO를 하나 만들고, 이를 매칭 시킬 Mapper 클래스를 추가한다.

- OrderMapper
```java
@Mapper(componentModel = "spring")
public interface OrderMapper {
    OrderDTO toDto(Order order);
}
```

`OrderDTO toDto(Order order);` 한줄로 Entity와 DTO 매핑이 완료되었다.  OrderMapperImpl을 확인해보면 아래와 같은 코드가 자동으로 생성된 것을 확인해볼 수 있다.

```java
@Component
public class OrderMapperImpl implements OrderMapper {

    @Override
    public OrderDto toDto(Order order) {
        if ( order == null ) {
            return null;
        }

        OrderDto orderDto = new OrderDto();

        orderDto.setPerson( order.getPerson() );
        orderDto.setOriginalPrice( order.getOriginalPrice() );

        return orderDto;
    }
```
단순히 코드 한줄로 매핑이 완료된 것을 확인할 수 있다. 심지어 다른 매퍼들에 비해 성능도 좋으니 점점 많이 쓰일 수 밖에 없다고 생각한다.

### MapStruct 주요 기능
하지만 필드명이 달라지거나, Entity에 없는 필드를 DTO에 추가해주어야 하는 등 여러가지 요구사항이 생길 수 있다. 

```java
@Mapper
public interface PersonMapper {
    @Mapping(target = "phone", ignore = true)
		@Mapping(target = "createdDate", expression = "java(new java.util.Date())")
    @Mapping(target = "homeAddress", source = "address")
    PersonDto toDto(Person person);
}
```
우선 매핑하고자 하는 필드명이 다를 경우 `@Mapping`에서 source와 target을 각각 지정하여 매칭시킬 수 있다. 위의 예시에서는 `Person`의 `address`가 `PersonDto`의 `homeAddress` 필드로 매핑된다.
`ignore`를 통해 특정 칼럼은 매핑하지 않도록 지정할 수 있다. 위의 예시의 경우 매핑 과정에서  `phone`은 무시된다. 
`expression`을 통해, 특정한 값이 매핑되도록 지정해줄 수 있다. 위의 예시에서는 `createdDate`가 항상 코드실행시점의 날짜로 지정된다.

### @Named, @QualifiedByName
expression을 통해 함수가 실행되도록 할 수 있지만, 특정 필드를 매핑하는 과정애서 커스텀한 함수를 사용하고 싶을 수 있다. 이럴 때, 위의 두 어노테이션을 사용하면 된다.
예를 들어 달러로 주문하는 사람이 있어서 원화로 변환해 주어야 하는 과정이 있다고 가정해보자. 그럴 경우 금액 변환 함수를 추가해야 할 것이다.

```java
@Mapper(componentModel = "spring")
public interface OrderMapper {
    OrderDto toDto(Order order);

    @Named("dollerToWon")
    public static double dollerToWon(int doller) {
        return doller * 1226;
    }
}
```
OrderMapper에 `dollerToWon`이라는 함수가 추가되었다. 물론 저 함수만 추가한다고 해서 자동으로 매핑과정에 포함되지 않는다.  

```java
@Mapper(componentModel = "spring")
public interface OrderMapper {
@Mapping(source = "dollerPrice", target = "originalPrice", qualifiedByName = "dollerToWon")
    OrderDto toDto(Order order);

    @Named("dollerToWon")
    static Integer dollerToWon(int dollerPrice) {
        return dollerPrice * 1200;
    }
}
```
`@mapping`의 `qualifiedByName`을 통해 매핑과정에 함수가 추가되도록 설정했다. 이제 OrderMapperImpl을 확인해서 원하는대로 금액 변환 함수가 추가되었는지 살펴보자.

```java
@Override
public OrderDto toDto(Order order) {
    if ( order == null ) {
        return null;
    }

    OrderDto orderDto = new OrderDto();

    if ( order.getDollerPrice() != null ) {
        orderDto.setOriginalPrice( OrderMapper.dollerToWon( order.getDollerPrice().intValue() ) );
    }
    orderDto.setPerson( order.getPerson() );

    afterMapping( order, orderDto );

    return orderDto;
}
```
의도대로 잘 추가된것을 확인할 수 있다.

### @AfterMapping, @BeforeMapping, @Context
자동으로 매핑되는 필드 외에 추가로 커스텀한 작업을 진행해주고 싶을 경우들이 있다. 이럴 때는`@AfterMapping`, `@BeforeMapping` 를 통해 진행할 수 있다. 

```java
@Mapper(componentModel = "spring")
public interface OrderMapper {

    OrderDto toDto(Order order);

    @AfterMapping
    default void afterMapping(Order order, @MappingTarget OrderDto orderDto) {
        int salePrice = (int) (order.getOriginalPrice() * order.getSaleRate());
        orderDto.setSalePrice(salePrice);
    }
}
```
`AfterMapping`을 통해 실행되는 코드는 다음과 같다. `toEntity` 함수에 `afterMapping` 함수가 추가된 것을 확인할수 있다. `@beforeMapping`을 사용할 경우 실행 순서만 앞으로 당겨지며 동작원리는 `@afterMapping`과 같다.

```
@Override
public OrderDto toDto(Order order) {
    if ( order == null ) {
        return null;
    }

    OrderDto orderDto = new OrderDto();
    orderDto.setPerson( order.getPerson() );

    afterMapping( order, orderDto );

    return orderDto;
}
```
생성된 OrderMapperImpl 클래스를 보면 `afterMapping` 함수가 추가된 것을 확인할 수 있다. 
사실 위의 어노테이션 외에도 `@Context`, `@InheritConfiguration` 등의 다양한 기능들이 있으며, Mapstruct 공식문서를 통해 확인할 수 있다.

### 주요 설정
- mapper 주요 설정
    - `Componentmodel`
        - 사용하고있는 프레임워크에 따라 cdi, jsr330, spring을 선택할 수 있다.
    - `unmappedTargetPolicy`
        - Source의 필드가 Target에 매핑되지 않을 때 정책이다.
        - IGNORE(default), WARN, ERROR를 사용할 수 있으며, ERROR로 지정할 경우, 매핑되지 않는 Source필드가 있으면 컴파일 에러가 발생한다.
    - `unmappedSourcePolicy`
        - Target의 필드가 매핑되지 않을 때 정책이다. IGNORE(default), WARN, ERROR를 통해 지정할 수 있다. ERROR로 지정할 경우, 매핑되지 않는 Target필드가 있으면 컴파일 에러가 발생한다.
    - `typeConversionPolicy`
        - 타입 변환 시 유실이 발생할 수 있을 때 정책이다. IGNORE(default), WARN, ERROR를 통해 지정할 수 있다.
    - `nullValueMappingStrategy`
        - Source가 null일 때의 정책이다.
        - RETURN_NULL(default), RETURN_DEFAULT 등을 선택할 수 있다.
    - `nullValuePropertyMappingStrategy`
        - Source의 필드가 null일 때 정책이다.
        - SET_TO_NULL(default), SET_TO_DEFAULT, IGNORE 등을 선택할 수 있다.
        

### Lombok과 Mapstruct를 같이 사용할 때 주의할 점
build.gradle에서 lombok보다 mapstruct의 어노테이션 프로세서 설정이 앞으로 가면 매핑이 제대로 되지 않으니 주의해야 한다.
Mapstruct는 매핑 과정에서 Getter, Setter을 통해 매핑을 처리한다. 그런데 Lombok 어노테이션 프로세서가 생성되기 전에 Mapstruct 어노테이션 프로세서가 필드를 매핑하려고 하면 Getter, Setter가 없는 private 필드에 접근을 할수가 없다. 따라서 필드가 매핑되지 않는다. 
build.gradle에서 annotationProcessor 순서를 아래와 같이 설정하면 오류를 피할 수 있다.
```java
implementation "org.mapstruct:mapstruct:${mapstructVersion}"
compileOnly 'org.projectlombok:lombok'

// 순서 주의!!
annotationProcessor 'org.projectlombok:lombok'
annotationProcessor "org.mapstruct:mapstruct-processor:${mapstructVersion}"
```

### 출처
- [Custom Mapper with MapStruct](https://www.baeldung.com/mapstruct-custom-mapper)
- [Object Mapping 어디까지 해봤니?](https://meetup.toast.com/posts/213)
- [What is MapStruct?](https://github.com/mapstruct/mapstruct#what-is-mapstruct)
