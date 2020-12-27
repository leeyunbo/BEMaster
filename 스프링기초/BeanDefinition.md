## 스프링 빈 메타 설정 정보 - BeanDefinition
### 스프링은 어떻게 Xml, 자바코드 등 다양한 설정 형식을 지원할까?  
![스크린샷 2020-12-27 오후 7 40 05](https://user-images.githubusercontent.com/44944031/103169196-f6482580-487c-11eb-84d8-ccc0ebc78c04.png)
1. `BeanDefinition`이라는 추상화가 존재하기 때문에 가능하다.
2. 즉, 역할(BeanDefinition)과 구현(AppConfig.class, AppConfig.xml, AppConfig.xxx)을 개념적으로 나눈 것이다. 
3. XML을 읽어서 또는 자바 코드를 읽어서 단순히 `BeanDefinition`을 만들면 된다.
4. 따라서 스프링 컨테이너는 자바 코드인지, XML인지 몰라도 된다. 오직 `BeanDefinition`만 알면 된다. 

### BeanDefinition이란? 
1. `BeanDefinition`을 빈 설정 메타정보라 한다.
2. `@Bean`, `<bean>` 당 각각 하나씩 메타 정보가 생성된다.
3. 스프링 컨테이너는 이 메타정보를 기반으로 스프링 빈을 생성한다. 

### 코드 레벨 
![스크린샷 2020-12-27 오후 7 41 23](https://user-images.githubusercontent.com/44944031/103169194-f47e6200-487c-11eb-97ad-be9550964809.png)
1. `AnnotationConfigApplicationContext`는 `AnnotationBeanDefinitionReader`를 사용하여 `AppConfig.class`를 읽고 `BeanDefinition`을 생성한다.
2. `GenericXmlApplicationContext`는 `XmlBeanDefinitionReader`를 사용하여 `AppConfig.xml` 설정 정보를 읽고 `BeanDefinition`을 생성한다. 
3. 만약 새로운 형식의 설정 정보가 추가되면, `XxxBeanDefinitionReader`를 만들어서 `BeanDefinition`을 생성하면 된다.

### 스프링 빈을 등록하는 방법
1. 직접 빈을 등록하거나 팩토리 빈을 통해 우회해서 등록하는 방법이 있다.
2. 자바 코드를 이용하여 등록하는 것이 팩토리 빈의 팩토리 메서드를 통해 등록하는 방법이다.(외부에서 팩토리 메서드를 호출하여 빈을 등록하는 방식)
3. FactoryBean은 `AppConfig.class`를 의미하며 FactoryMethod는 `AppConfig.class`에 존재하는 메서드들을 의미한다.

### BeanDefinition 설정 정보 
1. `BeanClassName` : 생성할 빈의 클래스 명 (자바 코드 설정 처럼 팩토리 빈을 활용하면 없음) 
2. `factoryBeanName` : 팩토리 빈의 이름, 예)AppConfig 
3. `factoryMethodName` : 팩토리 빈을 활용할 경우 빈을 생성할 팩토리 메서드, 예)memberService
4. `Scope` : 싱글톤이 기본 값이다.
5. `lazyInit` : 스프링 컨테이너를 생성할때 빈을 생성하는 것이 아닌, 실제 빈을 사용할 때 까지 최대한 생성을 지연처리 하는지 여부
6. `InitMethodName` : 빈을 생성하고, 의존관계를 적용한 뒤에 호출되는 초기화 메서드 명 
7. `DestroyMethodName` : 빈의 생명주기가 끝나서 파괴하기 직전에 호출되는 메서드 명
8. `Constructor arguments`, `Properties` : 의존관계 주입에서 사용한다. (팩토리 빈을 활용하면 없음)

<br/>

## 정리 
1. `BeanDefinition`을 개발자가 직접 생성하여 스프링 컨테이너에 등록할 수도 있지만, 거의 사용되지 않는다. 
2. `BeanDefinition`이 다양한 설정 정보의 추상화로 사용된다는 것을 알아야 한다.
