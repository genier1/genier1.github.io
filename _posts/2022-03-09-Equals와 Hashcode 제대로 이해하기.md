# Equals와 Hashcode 제대로 이해하기
equals와 hashcode는 매우 빈번하게 사용되는 메소드이지만, 내부 구현을 깊이 파악하고 쓰지 않고 있었다. 특히 Lombok에 있는 `@EqualsAndHashcode` 를 사용하는 경우가 많았으니 더더욱 그렇다. 이번 기회에 확실하게 정리하고자 한다.

## equals

equals는 기본적으로 동일성이 아닌 동등성을 비교하기 위한 메서드이다.
자바에서 동일성 비교는 == 연산자로 수행하며 값 뿐만 아니라 주소값까지 함께 비교한다.
반면, 동등성 비교는 equals()를 통해 수행하며 객체의 물리적 동일성이 아닌, 논리적 동등성을 비교한다. (기존 링크?)

```java
String text1 = new String("string");
String text2 = new String("string");

System.out.println(text1 == text2); // False
System.out.println(text1.equals(text2)); // True
```

논리적으로 보면 두개가 동일하지만, text1과 text2는 String Constant pool에 있는 주소값이 다르기 때문에 `==`으로 비교할 경우 동일하지 않은 객체로 취급된다. 동일성이 아닌 동등성을 비교하는 것이 중요하므로 모든 참조형 타입은 equals 메서드를 통해 비교를 수행하는 것이 원칙이다.

참고로 기본형 타입(Primitive Type)은 주소 값이 없다. float, double을 제외한 기본타입은 ==으로 비교하며, float와 double은 `Float.compare` `Double.compare` 로 비교한다. 

- 이펙티브자바에서는 Equals의 규칙을 5개로 규정하고 있다.
1. 반사성(reflexive*)* - null이 아닌 x에 대해 `x.equals(x)`는 true이다.
2. 대칭성(symmetric*)* - null이 아닌 x, y에 대해 `x.equals(y)`가 true이면 `y.equals(x)`도 true이다.
3. 추이성(transitive*)* - null이 아닌 x,y,z에 대해,`x.equals(y)`가 true이고 `y.equals(z)`가 true이면 `x.equals(z)`도 true이다.
4. 일관성(consistent*)* - null이 아닌 x, y에 대해,`x.equals(y)`를 여러번 반복하더라도 항상 true 또는 false의 같은 값을 반환한다.
5. not null - null이 아닌 모든 참조값 x에 대해 x.equals(null)은 false이다.

추가적으로 자바에서 0과 null은 객체로 취급하지 않는다. 이 부분이 종종 java에서 완전한 객체지향언어가 아니라고 지적을 받는 이유이기도 하다.

### equals 메소드 시그니처 올바르게 구현하기

`equals`를 구현할 때 아래와 같이 구현해도 괜찮을 것이라고 생각할 수 있다.

```java
public class Car {
    private String name;
    private String brand;

    public boolean equals(Car car) {
        return (this.getName() == other.getName() &&
                this.getY() == other.getY());
    }
}
```

겉보기에는 문제가 없어 보인다. 그러나 equals에 들어가는 변수는 `equals(Car car)`가 아닌 `equals(Object car)`가 되어야 한다.  만일 매개변수 타입을 Object가 아닌 클래스명으로 지정한다면 이후 Hashset에 추가할 때 문제가 된다. 

### 실제 구현

이펙티브 자바에 나오는 `equals`구현 로직은 다음과 같다.

1. == 연산자를 통해입력이  자기 자신의 참조인지 확인한다.
2. instanceof 연산자로 입력이 올바른 타입인지 확인한다.
3. 입력을 올바른 타입으로 형변환한다.
4. 입력 객체와 자기 자신의 대응되는 `핵심 필드`들이 모두 일치하는지 확인한다. 

```java
public class Car {
    private String name;
    private int price;

    protected boolean canEqual(Object other) {
        return other instanceof Car;
    }

    @Override
    public boolean equals(Object o) {
        if (o == this) return true;
        if (!(o instanceof Car)) return false;
        Car car = (Car) o;
        if (this.getName() == null ? car.getName() != null : !this.getName().equals(car.getName())) return false;
        if (this.price != car.price) return false;
        return true;
    }
}
```

### 상속과 equals

상속을 하면 기존의 Equals가 위반되기 쉽다. 

이 부분은 canEqual() 메서드 구현을 통해 상위 클래스가 하위 클래스와 같지 않음을 보장하는 방법이 있다. 상위클래스에 `canEqual`이 구현되어 있는 경우 하위 클래스에서 `canEqual`을 구현하거나 하지 않음으로써 상위클래스와의 동등성 비교를 허용하거나 허용하지 않을 수 있다.

```java
public class Vehicle {
    private String type;
}

public class Car extends Vehicle {
    private String name;
    private int price;
 
		// 상속시 equals를 방지하기 위한 canEqual 메소드
		protected boolean canEqual(Object other) {
        return other instanceof Car;
    }
}
```

## Hashcode

이펙티브자바에서는 Equals를 재정의하면 Hashcode도 재정의하라고 권장한다. 

### 왜 Hashcode를 재정의 해야 할까?

- `equals`가 true이면 `hashcode`도 같아야 한다.
    - `equals`가 false일 경우에는, `hashcode`가 반이펙티브 자바에 나오는 Hashcode 구현 로직은 다음과 같다.드시 다를 필요는 없다.
- `equals`가 사용하는 필드와 `hashcode`가 사용하는 필드를 동일하게 맞추어야 한다.
    - 그렇지 않으면 Hashset.contains 등이 올바르게 작동하지 않는다.

### Hashcode 구현 로직

이펙티브 자바에 나오는 `hashcode` 구현 로직은 다음과 같다.

1. int 변수 result 를 선언한 후 값 c로 초기화한다. 이때 c는 해당 객체의 첫 번째 핵심 필드를 단계 2.a 방식으로 계산한 해시코드다(여기서 핵심 필드란 equals 비교에 사용되는 필드이다.)
2. 해당 객체의 필드에 대해 다음과 같이 계산한다.
    1. 해당 객체의 나머지 핵심 필드 f 각각에 대해 다음 작업을 수행한다.
        1. 기본형 타입일 경우, Type.hashcode(f)를 수행한다. - 예) Integer.hashcode(5)
        2. 참조 타입 필드면서 이 클래스의 equals 메서드가 이 필드의 equals를 재귀적으로 호출해 비교한다면, 이 필드의 표준형(canonical representation)을 만들어 그 표준형의 hashCode를 호출한다. 필드의 값이 null 이면 0을 사용한다(다른 상수도 괜찮지만 전통적으로 0을 사용한다).
        3. 필드가 배열이라면, 핵심 원소 각각을 별도 필드처럼 다룬다. 이상의 규칙을 재귀적으로 적용해 각 핵심 원소의 해시코드를 계산한 다음, 단계 2.b 방식으로 갱신한다. 배열에 핵심 원소가 하나도 없다면 단순히 상수(0을 추천한다)를 사용한다. 모든 원소가 핵심 원소라면 Arrays.hashCode를 사용한다.
    2. 위에서 계산한 해시코드로 result를 갱신한다. 
3. result를 반환한다.

### 변경가능한 필드에 대해서는 equals/hashcode 구현 금지

변경 가능한 필드에 대해 `equals`, `hashCode`를 구현한 상태에서 해당 필드 값을 변경하면 동일 객체라도 hashCode가 바뀌게 된다.

또한, 필드 변경전에 특정 객체를 HashSet에 값을 넣은 후 필드 값을 변경한다면 HashSet에서 해당 객체를 찾을 수 없게 된다. hashCode로 bucket을 결정하는데 hashCode가 바뀌었기 때문이다.

```java
@Test
void equalsMutableTest() {
    Car car = new Car("소나타", 2000)
    int hashcode = car.hashCode(); // hashcode = 49191997

    car.setPrice(2200);
    int newHashcode = car.hashCode(); // newHashcode = 49203797

    Assertions.assertNotEquals(hashcode, newHashcode);
}
```

Car 객체의 Price를 2000에서 2200으로 변경했더니 Hashcode도 함께 바뀐것을 확인할 수 있다.

### Lombok의 @EqualsAndHashcode

흔히 많이 사용하는 Lombok에서의 `@EqualsAndHashcode` 역시 위의 규칙을 잘 지키고 있다. Lombok의 [EqualsAndHashCode 문서](https://projectlombok.org/features/EqualsAndHashCode) 에서 `@EqualsAndHashcode` 구현 원리를 확인할 수 있다.

### hashcode 구현 과정에서 소수를 곱하는 이유

이펙티브 자바나 Lombok의 해시코드 구현방식을 보면 31, 59 등의 소수를 곱한다. 소수를 곱하는 이유가 궁금하여 찾아봤더니 [이종립님 블로그](https://johngrib.github.io/wiki/Object-hashCode/#%EA%B7%B8%EB%9F%B0%EB%8D%B0-%EC%99%9C-31%EC%9D%84-%EA%B3%B1%ED%95%98%EB%8A%94-%EA%B1%B8%EA%B9%8C)에 이 부분에 대한 설명이 잘 되어있다.

결론만 말하자면, 짝수를 곱할 경우 비트 시프트가 발생하여 값이 변경되어 버릴 수 있으므로, 짝수는 곱하면 안된다. 여기에 소수에 대한 맹신이 더해져 Hashcode 구현 시 소수를 곱하고 있다. 

### 출처

- [java.lang.Object.hashCode 메소드 - 이종립님 블로그](https://johngrib.github.io/wiki/Object-hashCode/)
- [Java equals & hashCode](https://kwonnam.pe.kr/wiki/java/equals_hashcode)
- [How to Write an Equality Method in Java](https://www.artima.com/articles/how-to-write-an-equality-method-in-java)
- Effective Java 3/E
