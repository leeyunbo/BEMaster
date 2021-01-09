## 탐색 위치 지정 
### MyIncludeComponent 에노테이션 만들기 
```java
@Target(ElementType.TYPE) 
@Retention(RetentionPolicy.RUNTIME) 
@Documented
public @interface MyIncludeComponent { }
```
위의 에노테이션을 설정한 스프링 빈은 컴포넌트 스캔 대상에서 **포함**된다.

### MyExcludeComponent 에노테이션 만들기
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyExcludeComponent {
}
```
위의 에노테이션을 설정한 빈은 컴포넌트 스캔 대상에서 **제외**된다.

### excludeFilters, includeFilters 지정하기 
```java
    @Configuration
    @ComponentScan(
            includeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class),
            excludeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class)
    )
    static class ComponentFilterAppConfig {

    }
```
1. ComponentScan에서 `MyIncludeComponent` 에노테이션이 추가된 빈이 스프링 빈에 등록된다.
2. ComponentScan에서 `MyExcludeComponent` 에노테이션이 추가된 빈은 스프링 빈에서 제외된다. 
3. **`FilterType.ANNOTATION`은 Default 설정이기에 생략해도 동일하게 동작한다.**

### FilterType 옵션 
1. ANNOTATION : 기본값, 에노테이션을 인식하여 동작한다. ex) org.example.SomeAnnotation
2. ASSIGNABLE_TYPE : 지정한 타입과 자식 타입을 인식하여 동작한다. ex) org.example.SomeClass 
3. ASPECTJ : AspectJ 패턴을 사용한다. ex) org.example..*Service+ 
4. REGEX : 정규 표현식을 활용한다. ex) org\.example\.Default.*
5. CUSTOM : TypeFilter이라는 인터페이스를 구현하여 처리한다. ex) org.example.MyTypeFilter 

### 만약 BeanB.class가 MyExcludeComponent가 지정된 상태에서 BeanA.class도 제외하고 싶으면?
```java
@ComponentScan(
      includeFilters = {
          @Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class)},
      excludeFilters = {
          @Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class),
          @Filter(type = FilterType.ASSIGNABLE_TYPE, classes = BeanA.class) }
)
```

<br/>

## Filter 권장 사용법
1. `@Component` 에노테이션으로 충분하기에 `includeFilters`를 거의 사용할 일이 없으며 `excludeFilters`는 아주 가끔 사용된다.
2. 특히 최근 스프링 부트는 컴포넌트 스캔을 기본으로 제공하기에 옵션을 지정하는 것보다는 스프링의 기본 설정에 최대한 맞추어 사용하는 것을 권장한다. 
