## @Autowired를 통해 의존성 주입을 할 때, 타입이 같은 스프링 빈이 중복되어 존재한다면? 
> @Autowired는 첫번째로 타입으로 조회한다. <br/>
> 타입으로 조회했을 때 선택된 빈이 2개 이상이라면 문제가 발생한다. <br/>
> 어떻게 해결할 수 있을까? 

### 중복 예시 
```java
@Component
public class FixDiscountPolicy implements DiscountPolicy { ... }

@Component
public class RateDiscountPolicy implements DiscountPolicy { ... } 
     
     
@Component
@RequiredArgsConstructor
public class OrderServiceImpl implements OrderService {
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;
    ...
}
```
1. 위의 예시에서 `DiscountPolicy`를 상속하는 빈이 `FixDiscountPolicy`, `RateDiscountPolicy`가 존재하기에 중복 문제가 발생한다. 
2. **물론 이 상황에서 하위 타입으로 상세하게 지정해줄 수 있지만, 이러면 DIP를 위배하고 유연성이 저하된다.** 
3. 이러한 중복문제를 해결하는 방법은 여러가지가 존재한다. 

<br/>

## 중복 문제 해결 방법 
### @Autowired 필드명 매칭 
```java
@Component
@RequiredArgsConstructor
public class OrderServiceImpl implements OrderService {
    private final MemberRepository memberRepository;
    private final DiscountPolicy rateDiscountPolicy; // 필드명으로 가져올 스프링 빈의 이름을 지정한다. 
    ...
}
```
1. `@Autowired`는 먼저 타입 매칭을 시도하고, 중복된 빈이 존재하면 필드 이름, 생성자, 수정자, 메서드의 파라미터 이름으로 빈 이름을 추가 매칭한다.
2. 위에서는 `rateDiscountPolicy`로 필드명을 지정했기에 `RateDiscountPolicy.class`가 주입된다. 

### @Qualifier 사용 
```java
@Component
@Qualifier("subDiscountPolicy")
public class FixDiscountPolicy implements DiscountPolicy { ... }

@Component
@Qualifier("mainDiscountPolicy")
public class RateDiscountPolicy implements DiscountPolicy { ... } 
     
     
@Component
@RequiredArgsConstructor
public class OrderServiceImpl implements OrderService {
    private final MemberRepository memberRepository;
    Qualifier("mainDiscountPolicy") private final DiscountPolicy discountPolicy;
    ...
}
```
1. `@Qualifier`라는 구분자를 사용하는 방법이다.
2. **`@Qualifier`는 주입시 이정표를 붙여주는 것이지 빈 이름을 변경해주는 것은 아니다.** 
3. 각각 중복될 수 있는 빈 위에 `@Qualifier`를 붙여 특수한 이름을 부여한다. (부여될 필드 혹은 파라미터 옆에 `@Qualifier`를 붙여 가져올 빈의 이름을 명시한다.) 
4. 만약 `Qualifier("mainDiscountPolicy")`를 찾지 못한다면, `mainDiscountPolicy`라는 이름의 스프링 빈을 추가로 찾는다.
5. **`@Qualifier`는 `@Qualifier`를 찾는 용도로만 사용하는 것이 좋다.** 

### @Primary 사용 
```java
@Component
public class FixDiscountPolicy implements DiscountPolicy { ... }

@Component
@Primary
public class RateDiscountPolicy implements DiscountPolicy { ... } 
     
     
@Component
@RequiredArgsConstructor
public class OrderServiceImpl implements OrderService {
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;
    ...
}
```
1. 우선순위가 될 빈에 `@Primary`를 붙이게 되면 중복될 경우 해당 빈이 주입된다. 
2. 굉장히 편리한 방법이지만 어느정도 한계가 존재한다. 
3. 가장 많이 사용되는 방법이다. 

<br/>

## 정리 
### @Primary, @Qualifier 병행 사용 
1. 자주 사용하는 데이터베이스 커넥션을 획득하는 스프링빈과 특수한 경우에 사용되는 서브 데이터베이스의 커넥션을 획득하는 스프링 빈이 존재한다고 해보자.
2. **이때는 메인 데이터베이스 스프링 빈에 `@Primary`를 적용하여 편리하게 조회하도록 한다.**
3. **반면에 서브 데이터베이스 스프링 빈에는 `@Qualifier`를 통해 이름을 지정하여 가끔 명시적으로 조회하도록 한다.** 
4. 이렇게 되면 굉장히 깔끔하게 코드를 유지할 수 있다.

### 우선순위 
1. 위의 예시를 보면 알수있듯이 `@Qualifier`가 `@primary`에 비해 우선순위를 가진다.
2. **스프링은 자동보다는 수동이, 넓은 범위의 선택보단 좁은 범위의 선택이 우선순위를 가진다.**
