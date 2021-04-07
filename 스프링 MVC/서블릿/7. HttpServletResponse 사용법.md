# HttpServletResponse 기본 사용법 
### HttpServletResponse 역할
1. HTTP 응답 메시지 생성 
* HTTP 응답코드 지정
* 헤더 생성
* 바디 생성 

2. 편의 기능 제공
* Content-Type, 쿠키, Redirect

### HttpServletResponse를 사용해보자. 
#### 1. 편의 메서드를 사용하지 않고 지정 
```java
@WebServlet(name = “responseHeaderServlet”, urlPatterns = “/response-header”)
public class ResponseHeaderServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        //==[status-line]==//
        // 200 응답 코드 설정
        response.setStatus(HttpServletResponse.SC_OK);

        //==[response-header]==//
        response.setHeader(“Content-Type”, “text/plain;charset=utf-8”);
        // 캐시 무효화
        response.setHeader(“Cache-Control”, “no-cache, no-store, must-revalidate”);
        response.setHeader(“Pragma”, “no-cache”);
        response.setHeader(“my-header”, “hello”);

		  //==[message body]==//
        PrintWriter writer = response.getWriter();
        writer.println(“ok”);
    }
```


#### 2. 편의 메서드를 사용하여 지정 
```java
// HTTP 헤더 설정
private void content(HttpServletResponse response) {
    response.setContentType(“text/plain;charset-utf-8”);
    response.setCharacterEncoding(“utf-8”);
}
// 쿠키 설정
private void cookie(HttpServletResponse response) {
    Cookie cookie = new Cookie(“myCookie”, “good”);
    cookie.setMaxAge(600);
    response.addCookie(cookie);
}

// Redirect
private void redirect(HttpServletResponse response) throws IOException {
    response.sendRedirect(“/basic/hello-form.html”);
}
```


#### 결과
![8A2B01FF-C518-4E7A-B900-18C7CCE96FED](https://user-images.githubusercontent.com/44944031/113890056-cb1b8d80-97fe-11eb-9054-0891052001c9.png)
