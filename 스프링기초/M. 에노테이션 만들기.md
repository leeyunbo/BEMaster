## 에노테이션 만들기
> `@Qualifier("mainDiscountPolicy")`와 같이 문자를 적게되면 컴파일시 타입 체크가 이루어지지 않는다.  <br/>
> 문자는 컴파일 타임에 체크가 이루어지지 않기 때문이다. <br/>
> 그렇다면 이 문제를 어떻게 해결할 수 있을까? 

### 에노테이션 생성 
```java

/// MainDiscountPolicy.class
@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, ElementType.TYPE, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
@Qualifier("mainDiscountPolicy")
public @interface MainDiscountPolicy {

}

/// OrderServiceImpl.class 
public class OrderServiceImpl implements OrderService {
    
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    @Autowired
    public OrderServiceImpl(MemberRepository memberRepository, @MainDiscountPolicy DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}
```
1. 다음과 같이 에노테이션을 만들게 되면 해결할 수 있다. ( +) 오타를 방지할 수 있다. MainnnDiscountPolicy와 같은 오타)
2. 에노테이션은 상속이라는 개념이 없으며, 이렇게 여러 애노테이션을 모아서 사용하는 기능은 스프링이 지원해주는 기능이다. 
3. `@Qualifier` 뿐만 아니라 다양한 애노테이션들도 함께 조합하여 사용할 수 있다. 
4. **굉장히 좋은 기능이지만 무분별하게 사용하게 되면 유지보수에 굉장히 혼란을 부여할 수 있다.**
