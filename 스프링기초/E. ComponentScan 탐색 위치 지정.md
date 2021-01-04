## 탐색 위치 지정 
### basePackages
```java
@Configuration
@ComponentScan(
        basePackages = "hello.core.member",
        excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
)
```
1. basePackages를 통해 탐색할 패키지의 시작 위치를 지정할 수 있으며, 이 패키지를 포함하여 하위 패키지를 모두 탐색한다.
2. basePackages = {"hello.core", "hello.service"} 처럼 여러 시작 위치를 지정할 수 있다.
3. 만약 아무것도 지정하지 않으면 default로 @ComponentScan이 붙은 설정 정보 클래스의 패키지가 시작 위치가 된다.

### basePackageClasses 
```java
@Configuration
@ComponentScan(
        basePacakgesClasses = "AutoConfig.class",
        excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
)
```
1. 지정한 클래스의 패키지를 탐색 시작 위치로 지정한다. 
2. AutoConfig.class의 패키지가 basePackages로 지정되는 것과 같다. 

### 권장하는 방법 
1. 설정 정보 클래스의 위치를 프로젝트 최상단에 두어 따로 설정할 필요가 없도록 한다.
2. 최근 스프링 부트도 이 방법을 기본으로 제공한다. 
3. 즉, com.hello에 AppConfig.class 같은 설정 정보를 두고, @ComponentScan을 붙이면 basePackages를 지정할 필요가 없다.

