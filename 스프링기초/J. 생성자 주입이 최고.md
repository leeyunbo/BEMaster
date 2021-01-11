## 생성자 주입이 최고 
> 최근에는 DI 프레임워크 대부분이 생성자 주입을 권장한다. <br/>
> 되도록이면 생성자 주입을 사용하자!

### 불변 
1. 대부분 의존관계 주입은 애플리케이션 종료시점까지 의존관계 변경이 없다. 
2. 오히려 최대한 불변해야 한다. 
3. 만약 수정자 주입을 사용하게 되면 `setXxx` 메서드를 `public`으로 열어놔야 해서 불변하지 못하다.
4. 반면에 생성자 주입은 객체 생성시 딱 한번 호출되므로 이후에 호출되는 일이 없기에 불변하게 설계할 수 있다. 

### 누락 
```java
 @Test
  void createOrder() {
    OrderServiceImpl orderService = new OrderServiceImpl();
    orderService.createOrder(1L, "itemA", 10000); 
  }
```
1. `OrderServiceImpl`의 `createOrder()` 사용시 특정 객체가 필수적으로 필요하자고 해보자. 
2. 만약 수정자 주입을 이용했다면 순수 자바 코드로 테스트시에 위의 코드는 컴파일 오류가 발생하지 않는다. (런타임 오류 NPE가 발생한다.) 
3. 반면에 생성자 주입을 이용했다면 위의 코드는 컴파일 오류가 발생한다. (생성자 인자를 넣어주지 않았기 때문) 


### final 키워드
```java
@Component
public class OrderServiceImpl implements OrderService {
        
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;
        
    @Autowired
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
      this.memberRepository = memberRepository; 
    }
``` 
1. 생성자 주입을 사용하면 `final` 키워드를 사용할 수 있다. (초기화가 필수적이므로 생성자 주입에서는 사용 불가) 
2. `final` 키워드를 사용하게 되면 초기화를 해주지 않으면 컴파일 오류가 발생하기에 누락을 방지할 수 있다.
3. `final` 키워드를 사용하게 되면 초기화가 된 후 런타임에 의존관계가 바뀔 우려가 없어 불변을 유지할 수 있다.
* **참고) 컴파일 오류는 세상에서 제일 좋은 오류다.**

<br/>

## 정리 
1. 생성자 주입을 선택하는 이유는 순수한 자바 언어의 특징을 잘 살릴 수 있는 방법이기 때문이다.
2. 기본으로 생성자 주입을 사용하고, 필수 값이 아닌 경우에는 수정자 주입 방식을 옵션으로 부여한다. (동시에 사용하면 된다!)
3. 항상 생성자 주입을 선택하고, 가끔 수정자 주입을 선택하는 것이 좋다. 
4. 순수 자바 코드 테스트를 할 수 없게 만드는 필드 주입은 절대로 사용하지 않는 것이 좋다.


