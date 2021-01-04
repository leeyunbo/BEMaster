## 탐색 위치 지정 
### basePackages
```java
@Configuration
@ComponentScan(
        basePackages = "hello.core.member",
        excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
)
```
1. `basePackages`를 통해 탐색할 패키지의 시작 위치를 지정할 수 있으며, 이 패키지를 포함하여 하위 패키지를 모두 탐색한다.
2. `basePackages = {"hello.core", "hello.service"}` 처럼 여러 시작 위치를 지정할 수 있다.
3. **만약 아무것도 지정하지 않으면 default로 `@ComponentScan`이 붙은 설정 정보 클래스의 패키지가 시작 위치가 된다.**

### basePackageClasses 
```java
@Configuration
@ComponentScan(
        basePacakgesClasses = "AutoConfig.class",
        excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
)
```
1. 지정한 클래스의 패키지를 탐색 시작 위치로 지정한다. 
2. `AutoConfig.class`의 패키지가 `basePackages`로 지정되는 것과 같다. 

### 권장하는 방법 
1. **설정 정보 클래스의 위치를 프로젝트 최상단에 두어 따로 설정할 필요가 없도록 한다.**
2. 최근 스프링 부트도 이 방법을 기본으로 제공한다. 
3. 즉, `com.hello`에 `AppConfig.class` 같은 설정 정보를 두고, `@ComponentScan`을 붙이면 `basePackages`를 지정할 필요가 없다.
4. 스프링부트는 기본적으로 스프링 부트의 대표 시작 정보인 `@SpringBootApplication`을 프로젝트 최상단에 두는 것이 관례이다. (`@SpringBootApplication` 안에 `@ComponentScan`이 들어있음) 

<br/> 

## 컴포넌트 스캔 기본 대상 
### 다양한 에노테이션들
1. @Component : 컴포넌트 스캔에서 사용 
2. @Controller : 스프링 MVC 컨트롤러에 사용, `Controller`로 인식한다. 
3. @Service : 스프링 비즈닉스 로직에 사용, 특별한 처리는 없지만 개발자가 에노테이션을 보고 메인 비즈니스 로직이라고 인식한다.
4. @Repository : 스프링 데이터 접근 계층에서 사용, 스프링 데이터 접근 계층으로 인식하고 데이터 계층의 예외(구체적)를 스프링 예외(추상적)으로 변환해준다. 
5. @Configuration : 스프링 설정 정보에서 사용, 스프링 설정 정보로 인식하고 싱글톤을 유지하도록 추가 처리를 한다.
> 이 모든 에노테이션의 소스 코드를 보면 `@Component`를 포함하고 있는 것을 알 수 있다. 

