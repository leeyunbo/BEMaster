## 웹 스코프 
### 웹 스코프란? 
1. 웹 스코프는 웹 환경에서만 동작한다.
2. 웹 스코프는 프로토타입 스코프와 다르게 스프링 빈이 종료시점까지 관리한다.
3. 따라서 종료 콜백 메서드를 활용할 수 있다 (`@PreDestroy`) 

### 웹 스코프 종류
1. request : HTTP 요청 하나가 들어오고 나갈때까지 유지되는 스코프, 요청마다 새로운 인스턴스가 생성되고, 관리된다. 
2. session : HTTP Session과 동일한 생명주기를 가지는 스코프이다.
3. application : 서블릿 컨텍스트와 동일한 생명주기를 가지는 스코프이다.
4. websocket : 웹 소켓과 동일한 생명주기를 가지는 스코프이다. 

<br/>

## request 스코프 
### request 스코프의 동작 방식 
![스크린샷 2021-01-23 오후 6 57 08](https://user-images.githubusercontent.com/44944031/105575159-d12bd180-5dac-11eb-85fc-78f56784710d.png)
1. gradle에 'org.springframework.boot:spring-boot-starter-web' 웹 라이브러리를 추가해야한다. (스프링 부트는 톰켓 서버를 할용하여 웹서버와 스프링을 함께 실행) 
2. 동시에 여러 요청이 들어오면 각기 다른 인스턴스를 생성하고 관리한다. 
3. 만약 어떤 요청에 의하여 서비스가 호출되면 그 서비스는 그 요청과 동일한 인스턴스를 가라킨다. 

### request 스코프의 문제점 
```java
// MyLogger.class 
@Component
@Scope(value = "request")
public class MyLogger {

    private String uuid;
    private String requestURL;
    ...
}

// LogDemoController.class
@Controller
@RequiredArgsConstructor
public class LogDemoController {

    private final LogDemoService logDemoService;
    private final MyLogger myLogger;
    
    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {
    ...
}
```
1. 위의 코드를 실행하여 요청을 전송하면 오류가 발생한다.
2. 왜냐하면 스프링 어플리케이션을 실행하는 시점에 싱글톤 빈은 생성하여 주입이 가능하지만, request 스코프 빈은 아직 생성이 되지 않아 주입이 불가능하다. 
3. 실제 고객 요청이 와야지만 생성이 되기 때문이다. 

## request 스코프 문제점 해결 방법 - Provider와 proxy-mode  
### Provider 
```java
@Controller
@RequiredArgsConstructor
public class LogDemoController {

    private final LogDemoService logDemoService;
    private final ObjectProvider<MyLogger> myLoggerProvider;
    
    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {
        MyLogger myLogger = myLoggerProvider.getObject();
        String requestURL = request.getRequestURL().toString();
        myLogger.setRequestURL(requestURL);
        
        myLogger.log("controller test");
        logDemoService.logic("testId");
        return "OK";
    }
}
```
1. ObjectProvider 덕분에 ObjectProvider.getObject()를 호출하는 시점까지 request scope빈의 생성을 지연할 수 있다.
2. ObjectProvide.getObject()를 호출하는 시점은 요청이 진행중이기 때문에 request scope빈의 주입이 정상 처리된다.
3. 같은 HTTP 요청이면 어디서든 동일한 스프링 빈이 반환된다.

### proxy-mode 
```java
// MyLogger.class
@Component
@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class MyLogger {
...
}

// LogDemoController.class
@Controller
@RequiredArgsConstructor
public class LogDemoController {

    private final LogDemoService logDemoService;
    private final MyLooger mylogger;
    
    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {
        String requestURL = request.getRequestURL().toString();
        myLogger.setRequestURL(requestURL);
        
        myLogger.log("controller test");
        logDemoService.logic("testId");
        return "OK";
    }
}
```
1. `proxyMode = ScopedProxyMode.TARGET_CLASS`를 추가하면 오류를 방지할 수 있다. (적용 대상이 인터페이스면 `ScopedProxyMode.INTERFACES` 선택) 
2. 이렇게 되면 `MyLogger`를 상속받은 가짜 프록시 클래스를 만들어 미리 주입시켜 오류를 방지한다. 

### 프록시 객체 동작 방법 
![스크린샷 2021-01-23 오후 8 16 17](https://user-images.githubusercontent.com/44944031/105576686-df331f80-5db7-11eb-8ed8-67b33957c6a4.png)
1. 이전에 언급했던 CGLIB라는 라이브러리로 바이트 코드를 조작하여 MyLogger 클래스를 상속받은 가짜 프록시 객체를 만들어 생성한다. 
2. 실제로 http 요청이 일어난 후에도 이 가짜 객체가 그대로 이용되는데, 왜냐하면 그 내부에 진짜 빈을 요청하는 위임 로직이 존재하기 때문이다. 
3. 즉, 클라이언트 입장에서는 이 객체가 가짜 프로시 객체인지 진짜 객체인지 모르게 동일하게 사용할 수 있다.(다형성, 가짜 프록시 객체의 `logic()` 메서드를 호출하면 실제 객체 `logic()` 메서드가 호출됨)
