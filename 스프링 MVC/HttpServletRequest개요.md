# HttpServletRequest - 개요 

## HttpServletRequest 역할 
1. HTTP 요청 메시지를 직접 파싱하면 어려울 것 
2. 서블릿은 개발자가 손쉽게 요청 메시지를 활용할 수 있도록 대신 파싱함 
3. 이 객체에 담아서 제공함 

### HttpServletRequest 기본 제공 기능
HTTP 요청 메시지 
```
POST /save HTTP/1.1
Host: localhost:8080
Content-Type: application/x-www-form-urlencoded

username=kim&age=20 
```
1. Start Line `POST /save HTTP/1.1.. Host: localhost:8080`
* HTTP 메소드
* URL
* 쿼리 스트링
* 스키마, 프로토콜 

2. 헤더 `Content-Type:application/x-www-form-urlencoded`
* 헤더 조회

3. 바디 `username=kim&age=20`
* form 파라미터 형식 조회 `request.getParameter()…`
* message body 데이터 직접 조회

-> 위의 정보를 객체에 담아 제공한다.  이뿐만 아니라 `HttpServletRequest` 객체는 추가로 여러가지 부가기능도 제공한다. 

### 임시 저장소 기능 
1. 해당 요청이 끝날때 까지 유지되는 임시 저장소 기능 
* 저장 : `request.setAttribute(name, value)` 
* 조회 : `request.getAttribute(name)` 

### 세션 관리 기능 
1. `request.getSession(create: true)` 




