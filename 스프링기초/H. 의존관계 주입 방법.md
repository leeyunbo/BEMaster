## 의존관계 주입 방법 
### 생성자 주입
```java
    private final MemberRepository memberRepository; 
    private final DiscountPolicy discountPolicy; 
    
    @Autowired
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
```
1. **생성자 호출시점에 딱 1번만 호출되는 것이 보장된다.** 
2. 따라서 **불변, 필수** 의존관계에서 사용된다.
3. 필수 : 생성자 인자는 앵간하면 값이 채워져있는게 관례이다. (`private final`이 붙은 변수는 값이 무조건 채워져 있어야 함)   
4. 불변 : 한번 값이 채워진 후, 바뀌면 안되기 때문에 `Getter`나 `Setter`에 의해 변경되지 않도록 봉쇄해야 한다. 
5. 생성자가 1개면 `autowired`를 생략해도 된다. 

### 수정자 주입 
```java
    @Autowired  
    public void setMemberRepository(MemberRepository memberRepository) {
        this.memberRepository = memberRepository; 
    }
        
    @Autowired
    public void setDiscountPolicy(DiscountPolicy discountPolicy) {
        this.discountPolicy = discountPolicy; 
    }
```
1. `setXxx`, `getXxx`와 같은 수정자에 사용한다.  
2. **선택, 변경** 가능성이 있는 의존관계에 사용한다.
3. 즉, 스프링 빈에 인자로 들어올 객체들이 존재하지 않을 수 있는 상황에 사용한다. 
4. `@Autowired`의 기본 동작은 주입할 대상이 없으면 오류가 발생하므로, 주입할 대상이 없어도 동작하게 만드려면 `@Autowired(required = false)`로 지정하면 된다.

### 필드 주입 
```java
    @Autowired private final MemberRepository memberRepository; 
    @Autowired private final DiscountPolicy discountPolicy; 
```
1. 이름 그대로 필드에 바로 주입하는 방법이다.
2. **코드가 간결하여 좋아보이지만 순수 자바에서는 테스트하지 못한다는 치명적인 단점이 있다.** 
3. 즉, `Spring Framework(DI 프레임워크)`가 존재하지 않으면 테스트를 할 수 없다. (순수 자바 테스트 코드에는 당연히 `@Autowired` 사용 불가) 
4. 테스트를 하기 위해서는 `Getter`, `Setter` 메서를 추가하여 사용할 수 있지만 사용하지 않는 것이 좋다. 
5. Spring Framework를 이용하여 테스트를 하는 테스트 코드나 `@Configuration` 스프링 설정 파일에서만 사용하는 경우도 있다. 

### 일반 메서드 주입 
```java
    @Autowired 
    void init(MemberRepository memberRepository, OrderServiceImpl, orderServiceImpl) {
      ...
    }
```
1. **일반 메서드를 통해 주입한다.**
2. 잘 사용하지 않는다.
