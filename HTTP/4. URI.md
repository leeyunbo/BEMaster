# URI(Uniform Resource Identifier)
## URI? URL? URN? 
#### URI 
* 로케이터, 이름 또는 둘다 추가로 분류될 수 있다.  
* URL, URN을 포함한다. 
* Uniform : 리소스 식별하는 통일된 방식  
* Resource : URI로 식별할 수 있는 모든 것, 자원  
* Identifer : 식별할 수 있는 정보 

#### URL 
* 우리가 일상에서 사용하는 주소 느낌
* 주로 이걸 사용함  
* 로케이터, 리소스의 위치 
* 리소스가 여기 있을꺼야! (변함) 
* URI와 URL이 거의 비슷함 (포함 관계) 

#### URN
* 그 리소스의 진짜 이름 
* 잘 사용안함, 찾기 힘듬 
* 이름, 리소스의 이름 
* 리소스 이름! (변하지 않음) 
* URN으로 실제 리소스를 찾을 수 있는 방법이 보편화 되지 않음 

## URL 문법
`Scheme://[userinfo@]host[:port][/path][?query][#fragement]`
1. 프로토콜 
2. userInfo : URL에 사용자 정보 포함, 사용X
3. Host : 도메인명 
4. PORT : 거의 생략 
5. Path : 리소스 경로, 계층구조  
6. Query : key=value 형태, 웹서버에 제공하는 파라미터, 문자형태 
7. Fragment : 잘 사용하지 않음, 서버에 전송되는 정보가 아님  


