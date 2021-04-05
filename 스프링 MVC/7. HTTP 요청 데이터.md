# HTTP 요청 데이터 - 개요 
> HTTP 요청 메시지를 통해 클라이언트에서 서버로 데이터를 전달하는 방법을 알아보자. 

### GET - 쿼리 파라미터 
1. 메시지 바디 없이, 쿼리 파라미터에 담아 전달 
2. Ex) 검색, 필터, 페이징 등에서 많이 사용됨
 
### POST - HTML Form
1. `Content-type:application/x-www-form-urlencoded`
2. 메시지 바디에 쿼리 파라미터 형식으로 전달 
3. 예) 회원 가입, 상품 주문, HTML Form 사용 
 
### HTTP message body에 직접 데이터를 담아 요청 
1. HTTP API에서 주로 사용, JSON, XML, TEXT 
2. 주로 JSON을 사용 
3. POST, PUT, PATCH 
