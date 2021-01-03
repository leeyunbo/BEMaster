## 컴포넌트 스캔
### 컴포넌트 스캔이란?
1. 지금까지 자바 설정 파일의 `@Bean`이나 XML의 `<bean>`을 통하여 직접 스프링 빈을 나열한다.
2. 만약 스프링빈이 수십, 수백개가 되면 일일히 등록하기도 힘들고 누락하는 문제도 발생한다. 
3. **따라서 스프링은 설정 정보가 없어도 자동으로 스프링 빈을 등록하는 컴포넌트 스캔 기능을 제공한다.**
4. 또 의존관계도 자동으로 주입하는 `@Autowired`라는 기능도 제공한다.

### @ComponentScan 이용하기
```java
@Configuration
@ComponentScan(
        excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
)
public class AutoAppConfig {

}
```
1. 컴포넌트 스캔을 사용하려면 먼저 `@CompoentScan`을 위와 같이 설정 정보에 붙여준다.
2. 설정 파일을 보면 기존의 파일과 다르게 스프링 빈과 관련된 정보가 하나도 없다. 
3. `excludeFilters`를 이용하여 `Configuration.class`를 지정한 이유는 기존에 작성해놨던 설정 정보를 스프링 빈에 등록하는 불상사를 막기 위함이다. (단순히 예제 실행을 위해서임) 

### @Component를 이용하여 스프링 빈으로 등록하기 
![스크린샷 2021-01-03 오후 7 51 52](https://user-images.githubusercontent.com/44944031/103476935-294e6400-4dfd-11eb-80a7-b7f473732bcb.png)
1. 컴포넌트 스캔은 이름 그대로 `@Component` 에노테이션이 붙은 클래스를 스캔하여 스프링 빈으로 등록한다.
2. `@Configuration`이 컴포넌트 스캔의 대상이 될 수 있는 이유는 `@Configuration` 소스코드를 열어보면 `@Component` 에노테이션이 붙어있기 때문이다.
3. 이때 스프링빈의 기본 이름은 클래스명을 사용하되 맨 앞글자만 소문자를 사용한다.
4. 만약 특정 이름을 부여하고 싶으면 `@Component("특정이름")`을 이용한다.

### Autowired로 의존관계 등록하기
![스크린샷 2021-01-03 오후 7 52 36](https://user-images.githubusercontent.com/44944031/103476944-408d5180-4dfd-11eb-96d9-3ac6f07dc980.png)
1. 원래 기존의 설정 파일에서는 직접 의존관계를 명시했지만, `ComponentScan`을 이용하면 명시할 방법이 없다.
2. **따라서 의존관계 주입도 클래스 파일 내부에서 `@Autowired`를 이용하여 지정한다.**
3. `@Autowired`를 지정하면, 스프링 컨테이너가 자동으로 해당 스프링 빈을 찾아 주입한다.
4. 예시의 `@Autowired`는 `getBean(MemberRepository.class)`와 같다.


