## Autowired 옵션 처리
> 주입할 스프링 빈이 없어도 동작해야 할 때가 있다. <br/>
> 만약 `@Autowired`만 사용하면 `required`의 `default`가 `true`이기에 주입 대상이 없다면 오류가 발생한다. <br/>
> 따라서 옵션 처리를 통해 오류가 발생하지 않도록 해야한다. <br/>

### 옵션 처리 방법 예시 
```java
    static class TestBean {

        // 참고) Member는 스프링 빈이 아니다.
        @Autowired(required = false)
        public void setNoBean1(Member noBean1) {
            System.out.println("noBean1 = " + noBean1);
        }

        // 의존 관계가 없으면 호출은 되는데, null로 채워짐
        // 생성자에서도 사용 가능
        @Autowired
        public void setNoBean2(@Nullable Member noBean2) {
            System.out.println("noBean2 = " + noBean2);
        }

        // 의존 관계가 없으면 호출은 되는데, Optional.empty로 채워짐
        // 생성자에서도 사용 가능
        @Autowired
        public void setNoBean3(Optional<Member> noBean3) {
            System.out.println("noBean3 = " + noBean3);
        }

    }
```
1. @Autowired(required=false) : 자동 주입할 대상이 없으면 수정자 메서드 자체가 호출이 안된다. 
2. org.springframework.lang.@Nullable : 자동 주입 대상이 없으면 메서드는 호출되지만 `null`이 입력된다.
3. Optional<> : 자동 주입할 대상이 없으면 `Optional.empty`가 입력된다. 
> 참고 : `@Nullable`, `Optional`은 스프링 전반에 걸쳐 지원되기에 생성자 자동 주입에서 특정 필드에만 사용해도 가능하다.

* **Optional<> : Wrapper Class로 NPE를 제공되는 메소드로 간단히 회피할 수 있음 (복잡한 조건문 없이)**
