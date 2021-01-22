## 빈 스코프 
### 빈 스코프란? 
1. **스프링 빈이 존재할 수 있는 범위이다.** 
2. 보통 스프링 빈이 생성되면 컨테이너가 종료될 때까지 유지되는데, default scope가 `singleton`이기 때문이다. 

### 스프링이 제공하는 스코프 
1. singleton : default scope이며, 스프링 컨테이너의 시작과 종료까지 유지되는 가장 넓은 범위의 스코프이다.
2. prototype : 스프링 컨테이너가 빈 생성, 의존관계 주입, 초기화 까지만 관여하고 그 이후에는 클라이언트가 관리하는 스코프이다. 
3. 웹 관련 스코프 
- request : 클라이언트의 요청이 들어와서 나갈때 까지 유지되는 스코프이다.
- session : 세션이 생성되고 사라질 때 까지 유지된다.
- application : 웹의 `Servlet Context`와 같은 범위로 유지된다. <br/>
**참고)** Servlet Context : 하나의 서블릿이 서블릿 컨테이너와 통신하기 위해 사용되어지는 메서드들을 가지고 있는 클래스 

<br/>

## 프로토타입 스코프 
### 프로토타입 스코프란? 
```java
@Scope("prototype") 
@Component 
PrototypeBean HelloBean() {
  return new HelloBean(); 
}
```
1. 프로토타입의 빈으로 등록하게 되면 스프링 컨테이너는 해당 빈을 조회시 항상 새로운 인스턴스를 생성하여 반환한다. 
2. 항상 같은 인스턴스를 반환하는 싱글톤과 확연히 다르다. 
3. 클라이언트가 해당 빈을 조회하게 되면 스프링 컨테이너는 조회할 때마다 새로운 빈을 생성하고 의존관계 주입, 초기화를 마친 후 반환하다.
4. 정리하자면, 스프링 컨테이너는 생성, 주입, 초기화 까지만 관여하고 그 이후에는 클라이언트에게 전격으로 관리를 맡기게 된다. <br/> 
**참고)** 초기화까지만 관여하므로 @PreDestory와 같은 종료 콜백 메서드가 호출되지 않는다. 대신 클라이언트가 직접 종료 콜백 메서드를 호출한다. 


### 프로토타입 스코프 - 싱글톤 빈과 함께 사용시 문제점 
1. 프로토타입 스코프는 싱글톤 빈과 함께 사용하게 되면 제대로 동작되지 않는다. 

```java
   @Scope("singleton")
    static class ClientBean {
        @Autowired
        ProtypeBean prototypeBean 
        ...
    }
    
      @Scope("prototype")
    static class PrototypeBean {
    ...
    }
```
1. 위와 같이 싱글톤 스코프와 프로토타입 스코프를 함께 사용하면 프로토타입 스코프가 제대로 동작되지 않는 문제가 발생한다.
2. `ClientBean`이 싱글톤 스코프이기 때문에, 한번 의존관계 주입이 일어난 후에 절대 바뀌지 않는다. 
3. 즉, `prototypeBean`이 프로토타입 스코프이지만 `ClientBean`은 무조건 한개 밖에 존재하지 않기 떄문에, 새로운 인스턴스가 생성되지 않고 계속 유지된다. 
4. 만약에 `ClientBeanA`, `ClientBeanB`와 같이 여러개의 클래스가 존재한다면 `prototypeBean`의 기능은 동작은 할 것이다. (하지만 클래스의 갯수에 한정된다.) 
> 개발자가 프로토타입 스코프를 사용하는 이유는 의존관계 주입때만 새로운 빈이 주입되는 것이 아니라 사용할 때마다 새로운 빈을 생성해서 사용하는 것을 원하는 것이다.<br/>
> 이 문제를 어떻게 해결할 수 있을까? 

<br/>

## 프로토타입 스코프와 싱글톤 스코프의 병행 문제 해결 방법 
### Dependency Lookup
1. 의존관계를 외부에서 주입 받는게 아니라 직접 필요한 의존관계를 주입받는 것을 `Dependecy Lookup` 이하 DL이라고 한다. 
2. 병행 문제를 해결하기 위해서는 지정한 프로토타입 빈을 컨테이너에서 대신 찾아주는 DL 정도의 기능을 제공하는 무엇인가가 있으면 된다. 
3. 즉, 스프링 컨테이너가 자동으로 주입해주는 것이 아닌 개발자가 직접 지정한 프로토타입 빈을 가져와서 주입해주는 기능을 말한다.

### Spring이 제공하는 ObjectFactory, ObjectProvider 
```java
    static class ClientBean {
        @Autowired
        private ObjectProvider<PrototypeBean> prototypeBeanProvider;

        public int logic() {
            PrototypeBean prototypeBean = prototypeBeanProvider.get();
            prototypeBean.addCount();
            int count = prototypeBean.getCount();
            return count;
        }
    }
```
1. 지정한 빈을 컨테이너에서 대신 찾아주는 DL을 제공하는 것이 `ObjectProvider`이다. 
2. 과거에는 `ObjectFactory`를 사용했지만 현재는 이를 상속하여 더 많은 기능을 제공하는 `ObjectProvider`가 만들어졌다. 
3. Spring에서 제공하는 기능이기에, Spring에 의존적이다. 
4. 별도의 라이브러리가 필요 없다. 

### Java 표준(JSR-330) Provider
```java
    static class ClientBean {
        @Autowired
        private Provider<PrototypeBean> prototypeBeanProvider;

        public int logic() {
            PrototypeBean prototypeBean = prototypeBeanProvider.get();
            prototypeBean.addCount();
            int count = prototypeBean.getCount();
            return count;
        }
    }
```
1. `JSR-330` 자바 표준에서 제공하는 DL 라이브러리이다. 
2. 자바 표준이기에, Spring 컨테이너 뿐만 아니라 다양한 컨테이너에서 사용할 수 있다.
3. Spring에 제공하는 것이 아니기 때문에, gradle에 `javax.inject:javax.inject:1` 라이브러리를 추가 해야한다. 
4. `Provider`는 `ObjectProvider`와 다르게 딱 DL 기능만 제공한다. 
5. 자바 표준이므로 단위테스트를 만들거나 mock 코드를 만들기는 더욱 더 쉬워진다. 

<br/>

## 정리 
1. 사실 싱글톤 빈으로 거의 해결이 가능하기 때문에 프로토타입 빈을 사용하는 경우는 드물다. 
2. 만약 DL 기능을 활용해야 한다면 편의 기능을 추가적으로 제공하는 Spring의 `ObjectProvider`를 사용하는 것이 편리하다. 
3. 물론 그럴 일은 거의 드물지만, 다양 컨테이너에서 사용해야 한다면 자바 표준 `Provider`를 사용하는 것이 맞다.
4. DL 기능과 같이 자바 표준과 스프링 사이에 겹치는 경우가 많은데, 대부분 스프링이 제공하는 것이 좀 더 편리하므로 다른 컨테이너를 사용할 일이 없다면 스프링 제공 기능을 사용하는 것이 좋다.


