## 의문점  
### 코드 상으로는 세번 생성되는 memberRepository 
```java
@Configuration
public class AppConfig {

    @Bean
    public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }

    @Bean
    public OrderService orderService() {
        return new OrderServiceImpl(memberRepository(), discountPolicy());
    }
```
1. 다음 코드의 `orderService()`와 `memberService()`를 보면 둘다 `memberRepository()`를 호출한다.
2. 그렇다면 자바 코드 상으로는 `new` 키워드를 두번 호출하는 것인데 싱글톤 패턴이 유지가 될까?
3. 심지어 코드 상으로만 보면 Spring Container에 빈을 등록하는데 한번, `new` 키워드 두번이기에 총 세번이 호출된다.
4. 테스트를 해본 결과 동일한 객체가 반환된다. 즉, 싱글톤이 유지된다. 스프링 컨테이너는 어떻게 싱글톤을 유지하는 걸까?

<br/>

## 스프링 컨테이너의 해결책 
### CGLIB 바이트코드 조작 라이브러리 
```java
    @Test
    void configurationDeep() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
        AppConfig bean = ac.getBean(AppConfig.class);

        System.out.println("bean.getClass() = " + bean.getClass());
        // bean.getClass() = class hello.core.AppConfig$$EnhancerBySpringCGLIB$$4e1a4a11 
    }
```
1. 위의 코드를 실행해보면 `bean.getClass() = class hello.core.AppConfig$$EnhancerBySpringCGLIB$$4e1a4a11` 가 출력된다.
2. 원래라면 `패키지.AppConfig.class`가 출력 되어야 한다. 왜 이럴까? 
3. 스프링이 `CGLIB 바이트코드 조작 라이브러리`를 활용하여 `AppConfig.class`를 상속받은 임의의 다른 클래스를 만들고, 그 다른 클래스를 스프링 빈으로 등록한 것이다.

### CGLIB 바이트코드 조작 라이브러리를 이용해 생성된 클래스 
![스크린샷 2020-12-31 오후 9 16 36](https://user-images.githubusercontent.com/44944031/103410163-7fc56380-4bad-11eb-877b-25b862ffeeae.png)
1. 상속받아 새로 생성되어 등록된 클래스가 싱글톤이 보장되도록 해준다.
2. 즉, 해당 클래스에는 싱글톤을 유지하도록하는 로직이 있을 것이다. 

### 자동으로 생성되는 동적 로직 
```java
@Bean
    public MemberRepository memberRepository() {
        if (memoryMemberRepository가 이미 스프링 컨테이너에 등록되어 있으면?) { 
            return 스프링 컨테이너에서 찾아서 반환;
        } else { //스프링 컨테이너에 없으면
            기존 로직을 호출해서 MemoryMemberRepository를 생성하고 스프링 컨테이너에 등록 
            return 반환
        } 
    }
```

1. `@Bean`이 붙은 메서드마다 스프링 빈이 존재하면 존재하는 빈을 반환하는 코드가 동적으로 만들어진다.
2. 덕분에 싱글톤이 무조건 보장되는 것이다. 

<br/>

## 만약 설정파일에 @Configuration을 붙이지 않으면 어떻게 될까?
1. `CGLIB 바이트코드 조작 라이브러리`를 활용하여 코드를 동적으로 생성하는 과정이 사라진다.
2. 즉, 싱글톤이 보장되지 않는 것이다. 
3. 위의 의문점처럼 스프링 빈에는 등록되나 총 세번이나 객체가 생성될 것이다.

