## 빈 생명주기 콜백 지원 방법  
### 인터페이스 InitializingBean, DisposableBean
```java

public class NetworkClient implements InitializingBean, DisposableBean {
    
    // 빈이 생성될 때 호출되는 메서드 
    @Override
    public void afterPropertiesSet() throws Exception {
        connect();
        call("초기화 연결 메시지");
    }

    // 빈이 파괴될 때 호출되는 메서드
    @Override
    public void destroy() throws Exception {
        disconnect();
    }
}
```
1. `InitializingBean`은 초기화를 지원하며, `DisposableBean`은 소멸을 지원한다. 
2. **코드 레벨에서 사용하는 인터페이스이기 때문에, 내가 고칠 수 없는 라이브러리(클래스 파일)에는 적용할 수 없다.**
3. 초기화, 소멸 메서드의 이름이 정해져 있어 변경할 수 없다. 
4. 모든 코드를 설계할 때 해당 스프링 전용 인터페이스에 의존적으로 설계를 해야 한다. 
5. 2003년에 만들어졌으며, 거의 사용되지 않는다. 

### @Bean 초기화, 소멸 메서드 지정 
```java
public class NetworkClient {
    // 빈이 생성될 때 호출되는 메서드 
    public void init(){
        connect();
        call("초기화 연결 메시지");
    }

    // 빈이 파괴될 때 호출되는 메서드
    public void destroy(){
        disconnect();
    }
}

@Configuration
static class LifeCycleConfig {
    @Bean(initMethod = "init", destroyMethod = "close")
    public NetworkClient networkClient() {
        NetworkClient networkClient = new NetworkClient();
        networkClient.setUrl("http://hello-spring.dev");
        return networkClient;
   }
}
```
1. 메서드 이름을 자유롭게 지정할 수 있다.
2. 스프링 빈이 스프링의 특정 인터페이스에 의존하지 않는다. 
3. **코드 레벨이 아닌 설정 정보에서 지정해주기 때문에 코드를 수정할 수 없는 외부 라이브러리(클래스 파일)에서도 사용할 수 있다.** 

**참고)** 
> `@Bean`의 `destroyMethod`의 기본 값은 `"(inffered)"`(추론)으로 등록되어 있다. <br/>
> 이 추론 기능은 `close`, `shutdown`이라는 이름의 메서드를 자동으로 호출해준다. <br/>
> 만약 이 추론 기능을 사용하기 싫으면 `""` 공백으로 설정하면 된다. 

### 애노테이션 @PostConstruct, @PreDestroy 
```java
public class NetworkClient {
    // 빈이 생성될 때 호출되는 메서드 
    @PostConstruct
    public void init(){
        connect();
        call("초기화 연결 메시지");
    }

    // 빈이 파괴될 때 호출되는 메서드
    @PreDestroy
    public void destroy(){
        disconnect();
    }
}
```
1. 최신 스프링에서 가장 권장하는 방법이다. 
2. 스프링에 종속적인 기술이 아닌, `JSR-250`라는 자바 표준이여서 다양한 컨테이너에서 동일하게 동작한다. 
3. 컴포넌트 스캔에 가장 잘 어울린다.
4. **유일한 단점은 외부 라이브러리에 적용하지 못하여, `@Bean` 등록 기능을 함께 사용하도록 한다.** 

<br/>

## 정리 
1. **`@PostConstruct`, `@PreDestroy` 애노테이션을 사용하자** 
2. 외부 라이브러리를 초기화, 종료해야 하면 `@Bean`의 `initMethod`, `destroyMethod` 설정을 사용하자.
