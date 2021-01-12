## 롬북
> 막상 개발을 해보면, 대부분 의존관계는 불변이다. <br/>
> 따라서 의존관계 작성시 생성자에 final 키워드를 대부분 사용하게 된다.
> 그렇다면 필드 주입처럼 좀 더 간단하게 작성할 수 있는 방법은 없을까?

### @RequiredConstructor
```java
@Component
@RequiredArgsConstructor
public class OrderServiceImpl implements OrderService{

    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    @Override
    public Order createOrder(Long memberId, String itemName, int itemPrice) {
        //멤버 등급을 체킹하기 위한 멤버 정보 호출
        Member member = memberRepository.findById(memberId);

        //할인 정책이 변경되어도 OrderService는 변경될 필요가 없음 (단일 책임의 원칙)
        int discountPrice = discountPolicy.discount(member, itemPrice);

        return new Order(memberId, itemName, itemPrice, discountPrice);
    }
}
```
1. 롬북 라이브러리가 제공하는 @RequiredConstructor를 사용하면 final이 붙은 필드를 모아서 생성자를 자동으로 만들어준다. 
2. 생성자를 직접 작성하여 @Autowired를 사용한 코드와 완전히 동일한 기능을 보여준다.
3. 롬북이 자바의 에노테이션 프로세서라는 기능을 이용해서 컴파일 시점에 생성자 코드를 자동으로 생성해준다.


## 정리 


