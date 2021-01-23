## 웹 스코프 
### 웹 스코프란? 
1. 웹 스코프는 웹 환경에서만 동작한다.
2. 웹 스코프는 프로토타입 스코프와 다르게 스프링 빈이 종료시점까지 관리한다.
3. 따라서 종료 콜백 메서드를 활용할 수 있다 (@PreDestroy) 

### 웹 스코프 종류
1. request : HTTP 요청 하나가 들어오고 나갈때까지 유지되는 스코프, 요청마다 새로운 인스턴스가 생성되고, 관리된다. 
2. session : HTTP Session과 동일한 생명주기를 가지는 스코프이다.
3. application : 서블릿 컨텍스트와 동일한 생명주기를 가지는 스코프이다.
4. websocket : 웹 소켓과 동일한 생명주기를 가지는 스코프이다. 

## request 스코프 
### request 스코프의 동작 방식 
![스크린샷 2021-01-23 오후 6 57 08](https://user-images.githubusercontent.com/44944031/105575159-d12bd180-5dac-11eb-85fc-78f56784710d.png)
1.

## 프로토타입 스코프 
### 프로토타입 스코프란? 
```java
@Scope("prototype") 
@Component 
PrototypeBean HelloBean() {
  return new HelloBean(); 
}
```



