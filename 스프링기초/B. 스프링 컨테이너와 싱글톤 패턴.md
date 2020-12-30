## 싱글톤 패턴 
### 싱글턴 패턴이 왜 필요할까? 
```java
public class AppConfig {

    public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
    }

    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }

    public OrderService orderService() {
        return new OrderServiceImpl(memberRepository(), discountPolicy());
    }

    public DiscountPolicy discountPolicy() {
        // return new FixDiscountPolicy();
        return new RateDiscountPolicy();
    }
}
```
1. 스프링이 없는 순수한 DI 컨테이너 `AppConfig`는 요청을 할 때 마다 객체를 새로 생성한다.
2. 만약 고객 트래픽이 초당 100이 나오면 초당 100개 객체가 생성되고 소멸된다. **(메모리 낭비)** 
3. 해결방안은 해당 객체가 딱 1개만 생성되고, 공유하도록 설계하면 된다. **(싱글턴 패턴)**

### 싱글톤 패턴이란?
```java
public class SingletoneService {

    private static final SingletoneService instance = new SingletoneService();

    public static SingletoneService getInstance() {
        return instance;
    }

    // 외부에서 객체 생성 방지
    private SingletoneService() {
    }

    public void logic() {
        System.out.println("싱글톤 객체 로직 호출");
    }

}
```
1. 클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인 패턴이다.
2. 딱 1개의 객체 인스턴스만 존재해야 하므로 `private` 생성자를 사용하여 외부에서 `new` 키워드를 사용하여 객체를 생성하지 않도록 막아야 한다.
3. `static` 영역에 객체 `instance`를 미리 하나 생성해서 올려둔다.
4. `getInstance()` 메서드를 통해서만 객체 인스턴스를 조회할 수 있다.
5. 객체를 클래스가 로딩되는 시점부터 생성하는 방법과 지연시켜 객체가 조회될 때 처음으로 생성하는 지연 방법이 있는데 위의 방법은 첫번째 방법이다.

### 싱글톤 패턴 문제점 
1. 싱글톤 패턴을 구현하는 코드 자체가 많이 들어간다.
2. 클라이언트가 구체 클래스의 `getInstance()`를 호출하므로 DIP를 위반한다.
3. `DIP`를 위반하므로 `OCP`를 위반할 가능성이 높다.
4. 테스트가 어렵다.
5. 내부 속성을 변경하거나 초기화 하기 어렵다.
6. `private` 생성자로 자식 클래스를 만들기 어렵다.
7. 위의 문제점을 생각하면 결국에 유연성이 떨어진다고 할 수 있다. 
8. 이러한 문제로 **안티패턴**으로 불리기도 한다.

<br/>

## 싱글톤 컨테이너 
### 스프링 컨테이너와 싱글톤 패턴
![스크린샷 2020-12-29 오후 9 14 47](https://user-images.githubusercontent.com/44944031/103283076-f120dd00-4a1a-11eb-9c15-bd92fc61298e.png)
1. 스프링 컨테이너는 싱글톤 패턴을 적용하지 않아도 객체 인스턴스를 싱글톤으로 관리한다.
2. 스프링 컨테이너는 **싱글톤 컨테이너** 역할을 하며, 싱글톤 객체를 생성하고 관리하는 기능을 **싱글톤 레지스트리**라 한다.
3. 이러한 이유 때문에 다음과 같은 싱글톤 패턴의 모든 단점을 해결하며 객체를 싱글톤으로 유지할 수 있다.
> 지저분한 코드가 필요없다.
> `DIP`, `OCP`를 위반하지 않으며 `private` 생성자가 필요없고 테스트하기에도 용이하다. 
4. 물론 스프링의 기본 빈 등록 방식은 싱글톤이지만, 변경할 수 있다.

<br/>

## 싱글톤 방식의 주의점
### 무상태로 설계하자. 
1. 싱글톤 방식은 여러 클라이언트가 하나의 같은 객체를 공유하기 때문에 **해당 객체는 상태를 유지해선 안된다.**
2. 특정 클라이언트에 의존적인 필드가 있으면 안된다.
3. 특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안된다.
4. 가급적 읽기만 가능해야 한다.
5. 필드 변수 대신에 자바에서 서로 공유되지 않는 지역변수, 파라미터, ThreadLocal 등을 사용해야 한다.
6. **필드에 공유 값을 설정하면 정말 큰 장애가 발생할 수 있다.**

### 예시 
``` java
public class StatefulService {

    private int price; // 상태를 유지하는 필드

    public void order(String name, int price) {
        System.out.println("name = " + name + " price = " + price);
        this.price = price; // 여기가 문제
    }

    public int getPrice() {
        return price;
    }
}

void statefulServiceSingleton() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);
        StatefulService statefulService = ac.getBean("statefulService", StatefulService.class);
        StatefulService statefulService2 = ac.getBean("statefulService", StatefulService.class);

        //ThreadA : A사용자가 10000원 주문
        statefulService2.order("userA", 10000);

        //ThreadB : B사용자가 20000원 주문
        statefulService.order("userB", 20000);

        //ThreadA : 사용자A 주문 금액 조회
        int price = statefulService.getPrice();
        System.out.println("price = " + price);

        // B사용자가 중간에 끼어들어서 기대하던 값이 나오지 않음 (같은 객체를 활용하다보니..)
        Assertions.assertThat(statefulService.getPrice()).isEqualTo(10000);
    }
```
1. `ThreadA`가 사용자A 코드를 호출하고 `ThreadB`가 사용자B 코드를 호출한다 가정하자.
2. `StatefulService`의 `price` 필드는 공유 필드이며 특정 클라이언트들에 의해 값이 변경된다. 
3. 사용자A가 주문한 후 계산값을 가져오기 전에 사용자B가 주문한 계산 값이 덮어씌어져 잘못된 값이 출력된다.
4. 이러한 이유로** 항상 스프링 빈은 무상태로 설계해야 한다.**

