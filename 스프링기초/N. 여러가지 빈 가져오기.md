## 여러가지 빈 가져오기 
> 한가지 빈만 뽑아오는 것이 아닌 특정 타입의 모든 빈이 필요한 경우가 있다.  <br/>
> 예를 들어 할인 서비스를 제공하는데, 클라이언트가 할인의 종류를 선택할 수 있다고 해보자. <br/>
> 그렇다면 이 문제를 어떻게 해결할 수 있을까? 

### List, Map 주입 
```java
    static class DiscountServce {
        private final Map<String, DiscountPolicy> policyMap;
        private final List<DiscountPolicy> policyList;

        @Autowired
        public DiscountServce(Map<String, DiscountPolicy> policyMap, List<DiscountPolicy> policyList) {
            this.policyMap = policyMap;
            this.policyList = policyList;
            System.out.println("policyMap = " + policyMap);
            System.out.println("policyList = " + policyList);
        }

        public int discount(Member member, int price, String discountCode) {
            DiscountPolicy discountPolicy = policyMap.get(discountCode);
            return discountPolicy.discount(member, price);
        }
    }
```
1. `DiscountService`는 `Map`으로 모든 `DiscountPolicy` 구현체를 주입받는다. 
2. `discount()` 메서드는 `discountCode`를 키로 이용하여 해당되는 할인 정책 빈을 가져와 실행한다. 
3. "rateDiscountPolicy" -> rateDiscountPolicy.class, "fixDiscountPolicy" -> fixDiscountPolicy.class 

<br/>

## 정리 
### 주입은 어떻게 이루어지는 것일까? 
1. **`Map<String, DiscountPolicy>` : map의 키에 스프링 빈의 이름을 넣어주고, 그 값으로 해당되는 스프링빈을 넣어준다.**
2. **`List<DiscountPolicy>` : 스프링 빈들이 주입된다.** 
3. 해당되는 타입의 빈들이 없다면 null이 아닌 요소가 없는 빈 Map, List가 주입된다.
