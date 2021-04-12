# HTTP 응답 데이터 - 단순 텍스트, HTML 
1. 단순 텍스트 응답 `writer.println(“OK”)`
2. HTML 응답


### HTTP 응답 데이터 - HTML 사용해보기 
```java
@WebServlet(name=“responseHtmlServlet”, urlPatterns = “/response-html”)
public class ResponseHtmlServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //Content-Type: text/html;charset=utf-8
        response.setContentType(“text/html”);
        response.setCharacterEncoding(“utf-8”);

        PrintWriter writer = response.getWriter();
        writer.println(“<html>”);
        writer.println(“<body>”);
        writer.println(“ <div>안녕?</div>”);
        writer.println(“<body>”);
        writer.println(“<html>”);
    }
}
```
