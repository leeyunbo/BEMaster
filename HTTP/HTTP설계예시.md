# HTTP API 설계 예시
## 회원 관리 시스템 
### API 설계 - POST 기반 등록 (컬렉션) -> 많이 사용한다. 
* 회원 목록 /members -> GET 
* 회원 등록 /members -> POST
* 회원 조회 /members/{id} -> GET 
* 회원 수정 /members/{id} -> PATCH, PUT, POST  (PATCH가 가장 BEST, 애매할 땐 POST) 
* 회원 삭제 /members/{id} -> DELETE  

<br/>

### API 설계 - POST 기반 등록 특징 
#### 1. 클라이언트는 등록될 리소스의 URI를 모른다. 
* POST /members 
#### 2. 서버가 새로 등록될 리소스의 URI를 생성해준다. 
* Location: /members/100. 
#### 3. 이런 형식을 컬렉션이라고 한다.
* 서버가 관리하는 리소스 디렉토리 
* 서버가 리소스의 URI를 생성하고 관리한다. 
* 여기서 컬렉션은 /members 

<br/>

### API 설계 - PUT 기반 등록 (스토어)
* 파일 목록 /files -> GET 
* 파일 조회 /files/{filename} -> GET 
* 파일 등록 /files/{filename} -> PUT  (파일이 있다면 지우고 덮어쓰고 없으면 저장)
* 파일 삭제 /files/{filename} -> DELETE 
* 파일 대량 등록 /files -> POST

<br/>

### API 설계 -> PUT 기반 등록 특징 
#### 1. 클라이언트가 리소스 URI를 알고 있어야 한다. 
* 파일 등록 /files/{filename} -> PUT  
* PUT /files/star.jpg.. 
#### 2. 클라이언트가 직접 리소스의 URI를 지정한다. 
#### 3. 이런 형식을 스토어라고 한다. 
* 클라이언트가 리소스 저장소를 관리한다. 
* 클라이언트가 리소스의 URI를 알고 관리한다.
* 여기서 스토어는 /files 

<br/>

### HTML FORM 사용  
* GET, POST만 지원한다. 
* AJAX를 사용하면 GET,POST 외에도 사용할 수 있긴 함
* 여기서는 GET, POST만 얘기하도록 하겠음 

<br/>

### HTML FORM 사용 - 설계 
* 회원 목록 /members -> GET
* 회원 등록 폼 /members/new -> GET 
* 회원 등록 /members/new or /members -> POST
* 회원 조회 /members/{id} -> GET 
* 회원 수정 폼 /members/{id}/edit -> GET (어쩔 수 없이 컨트롤 URI를 사용해야함!, 동사 말하는거 ㅋ) 
* 회원 수정 /members/{id}/edit or /members/{id} -> POST 
* 회원 삭제 /members/{id}/delete -> POST  (어쩔 수 없이 컨트롤 URI를 사용해야함!) 

<br/>

### HTML FORM 사용 - 컨트롤 URI 
* GET, POST만 사용할 수 있는 제약을 해결하기 위해 동사로 된 경로 사용 
* edit, delete, new… 
* HTTP 메서드로 해결하기 애매한 경우 사용한다. 
* 실무에서도 굉장히 많이 사용함 ㅎㅎ.. 애매한 경우가 너무 많음, 물론 자제! 최대한 리소스 URI 사용! 

<br/>
<br/>

## 정리 
### 참고하면 좋은 URI 설계 개념 
#### 1. 문서 
* 단일 개념 (파일 하나, 객체 인스턴스, 데이터베이스 row) 
* 예) /members/100, /files/star.jpg 

#### 2. 컬렉션 
* 서버가 관리하는 리소스 디렉터리 
* 서버가 리소스의 URI를 생성하고 관리
* 예) /members 

#### 3. 스토어 
* 클라이언트가 관리하는 리소스 저장소 
* 클라이언트가 리소스의 URI를 알고 관리 
* 예) /files/{filename} 

#### 4. 컨트롤러, 컨트롤 URI 
* 문서, 컬렉션, 스토어로 해결하기 어려운 추가 프로세스 실행 
* 동사를 직접 사용한다.
* 예) /members/{id}/delete 
* 최대한 컬렉션,스토어,문서로 해결하다가 도저히 안되면 컨트롤 URI를 활용한다. 
