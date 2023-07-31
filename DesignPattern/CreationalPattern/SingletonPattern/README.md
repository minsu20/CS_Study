# 싱글톤 패턴(Singleton Pattern)

싱글톤 패턴은 하나의 클래스가 오직 하나의 인스턴스만을 가지도록 설계하는 패턴이다. 이는 인스턴스 생성에 필요한 비용을 줄일 수 있지만, 결과적으로 높은 의존성을 가질 수 있어 이는 단점으로 작용한다.

## 스프링이 없는 순수한 DI 컨테이너 테스트

아래의 코드는 싱글톤이 아닌 경우, 즉, 요청을 할 때마다 새로운 객체가 생성되는 것을 보여주고 있다. 이는 고객 트래픽이 많을 경우, 많은 수의 객체가 생성되고 소멸되어 메모리 낭비가 크다는 것을 보여주는 예시이다.

```java
public class SingletonTest {
  @Test
  @DisplayName("스프링 없는 순수한 DI 컨테이너")
  void pureContainer() {
    AppConfig appConfig = new AppConfig();

    //1. 조회: 호출할 때 마다 객체를 생성
    MemberService memberService1 = appConfig.memberService();

    //2. 조회: 호출할 때 마다 객체를 생성
    MemberService memberService2 = appConfig.memberService();

    //참조값이 다른 것을 확인
    System.out.println("memberService1 = " + memberService1);
    System.out.println("memberService2 = " + memberService2);

    //memberService1 != memberService2
    assertThat(memberService1).isNotSameAs(memberService2);
  }
}

```

이 문제를 해결하기 위해, `같은 타입의 객체를 요구할 때 마다 항상 동일한 인스턴스를 제공하도록 설계`하는 싱글톤 패턴이 이용될 수 있다. 

## 싱글톤 패턴 구현

싱글톤 패턴은 클래스의 인스턴스가 하나만 생성되도록 하는 디자인 패턴이다. 이를 위해, private 생성자를 사용하여 외부에서 `new` 키워드를 통해 객체를 생성하는 것을 방지한다. 또한, 미리 생성된 객체 인스턴스는 static 메서드를 통해서만 조회할 수 있도록 한다.

```java
public class SingletonService {

  //1. static 영역에 객체를 딱 1개만 생성해둔다.
  private static final SingletonService instance = new SingletonService();

  //2. public으로 열어서 객체 인스턴스가 필요하면 이 static 메서드를 통해서만 조회하도록 허용한다.
  public static SingletonService getInstance() {
    return instance;
  }

  //3. 생성자를 private으로 선언해서 외부에서 new 키워드를 사용한 객체 생성을 못하게 막는다.
  private SingletonService() {
  }

  public void logic() {
    System.out.println("싱글톤 객체 로직 호출");
  }
}

```

하지만, 싱글톤 패턴은 여러 문제점이 있다.

## 싱글톤 패턴의 문제점

- 싱글톤 패턴을 구현하는 코드가 많이 필요하며, 이는 복잡성을 증가시킨다.
- 클라이언트가 구체 클래스에 의존하게 되어 DIP(Dependency Inversion Principle)를 위반한다.
- 이로 인해 OCP(Open-Closed Principle)를 위반할 가능성이 높아진다.
- 테스트가 어렵고, 내부 속성을 변경하거나 초기화하는 것이 어렵다.
- private 생성자로 인해 자식 클래스를 생성하는 것이 어렵다.
- 유연성이 떨어지며, 때때로 이는 안티 패턴으로 불리기도 한다.

→ 스프링 컨테이너는 이러한 싱글톤 패턴의 문제점을 해결하면서, 동시에 객체 인스턴스를 싱글톤으로 관리하는 기능을 제공하고, 이를 통해 개발자는 고성능과 메모리 효율을 얻을 수 있다!

## 싱글톤 컨테이너

스프링 컨테이너는 '싱글톤 컨테이너' 역할을 한다. 이는 스프링 컨테이너가 객체 인스턴스를 싱글톤으로 관리하며, 이를 '싱글톤 레지스트리'라고 한다. 싱글톤 패턴의 모든 단점을 해결하면서도, 객체를 싱글톤으로 유지할 수 있다. 이로 인해 코드가 복잡해지지 않으면서도, DIP, OCP, 테스트, private 생성자에 자유롭게 싱글톤을 사용할 수 있게 된다!

```java
@Test
@DisplayName("스프링 컨테이너와 싱글톤")
void springContainer() {
    ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

    //1. 조회: 호출할 때 마다 같은 객체를 반환
    MemberService memberService1 = ac.getBean("memberService", MemberService.class);
    //2. 조회: 호출할 때 마다 같은 객체를 반환
    MemberService memberService2 = ac.getBean("memberService", MemberService.class);

    //참조값이 같은 것을 확인
    System.out.println("memberService1 = " + memberService1);
    System.out.println("memberService2 = " + memberService2);

    //memberService1 == memberService2
    assertThat(memberService1).isSameAs(memberService2);
}

```

위 코드는 스프링 컨테이너가 싱글톤 객체를 잘 관리하고 있는 것을 보여준다. 동일한 서비스를 요청하더라도 항상 같은 인스턴스를 반환하고 있다.

## 주의점: 무상태(stateless)로 설계해야 한다!

싱글톤 패턴이든 스프링 컨테이너든, 객체 인스턴스를 하나만 생성해서 공유하는 싱글톤 방식은 여러 클라이언트가 하나의 같은 객체 인스턴스를 공유하므로, 상태를 유지하는 (stateful) 방식으로 설계하면 안됩니다. 대신, 무상태(stateless)로 설계해야 한다.

즉, 특정 클라이언트에 의존적인 필드나, 특정 클라이언트가 값을 변경할 수 있는 필드를 가지고 있으면 안된다. 가급적 읽기만 가능해야 하며, 필드 대신에 공유되지 않는 지역변수, 파라미터, ThreadLocal 등을 사용해야 한다.

아래 예제는 상태를 유지하는 방식의 설계에서 발생하는 문제를 보여준다.

```java
public class StatefulServiceTest {

    @Test
    void statefulServiceSingleton() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);
        StatefulService statefulService1 = ac.getBean("statefulService", StatefulService.class);
        StatefulService statefulService2 = ac.getBean("statefulService", StatefulService.class);

        //ThreadA: A사용자 10000원 주문
        statefulService1.order("userA", 10000);

        //ThreadB: B사용자 20000원 주문
        statefulService2.order("userB", 20000);

        //ThreadA: 사용자A 주문 금액 조회
        int price = statefulService1.getPrice();

        //ThreadA: 사용자A는 10000원을 기대했지만, 기대와 다르게 20000원 출력
        System.out.println("price = " + price);

        Assertions.assertThat(statefulService1.getPrice()).isEqualTo(20000);
    }

    static class TestConfig {

        @Bean
        public StatefulService statefulService() {
            return new StatefulService();
        }
    }
}

```

위 테스트에서는 A사용자와 B사용자가 각각 주문을 하고, 주문 금액을 조회하는 과정을 거치게 된다. 여기서 문제는 `StatefulService`의 `price` 필드가 공유되고 있어, 사용자 A가 조회하는 금액이 사용자 B의 주문 금액으로 변경되어버린다는 점이다.

이렇게 무상태를 유지하지 않고 공유 필드를 사용하면 심각한 장애를 유발할 수 있기에 이를 피하기 위해, 스프링 빈은 항상 무상태(stateless)로 설계해야 한다.

## AppConfig 클래스의 구성

`AppConfig` 클래스는 다음과 같이 구성되어 있다. 

```java
@Configuration
public class AppConfig {

      @Bean
      public MemberService memberService() {
          return new MemberServiceImpl(memberRepository());
      }

      @Bean
      public OrderService orderService() {
          return new OrderServiceImpl(
                  memberRepository(),
                  discountPolicy());
      }

      @Bean
      public MemberRepository memberRepository() {
          return new MemoryMemberRepository();
      }

      ...
}

```

여기서 `memberService()` 메서드와 `orderService()` 메서드는 `memberRepository()`를 호출하므로, 개별적으로 객체를 생성하면 `MemoryMemberRepository`는 총 세 번 생성되어야 한다.

## 테스트 코드

```java
public class ConfigurationSingletonTest {

      @Test
      void configurationTest() {
          ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
          MemberServiceImpl memberService = ac.getBean("memberService", MemberServiceImpl.class);
          OrderServiceImpl orderService = ac.getBean("orderService", OrderServiceImpl.class);
          MemberRepository memberRepository = ac.getBean("memberRepository", MemberRepository.class);

          // 모두 같은 인스턴스를 참고하고 있다.
          System.out.println("memberService -> memberRepository = " + memberService.getMemberRepository());
          System.out.println("orderService -> memberRepository  = " + orderService.getMemberRepository());
          System.out.println("memberRepository = " + memberRepository);

          // 모두 같은 인스턴스를 참고하고 있다.
          assertThat(memberService.getMemberRepository()).isSameAs(memberRepository);
          assertThat(orderService.getMemberRepository()).isSameAs(memberRepository);
      }
}

```

위의 테스트 코드를 실행하면 `memberService`와 `orderService`가 같은 `MemberRepository` 인스턴스를 참조하고 있음을 확인할 수 있습니다. 이는 스프링의 싱글톤 컨테이너가 잘 동작하고 있음을 의미한다.

## 실험을 통한 확인

이제 `AppConfig` 클래스에서 각 메서드가 호출되는 횟수를 확인해보자.

```java
@Configuration
public class AppConfig {

      @Bean
      public MemberService memberService() {
          //1번
          System.out.println("call AppConfig.memberService");
          return new MemberServiceImpl(memberRepository());
      }

      @Bean
      public OrderService orderService() {
          //1번
          System.out.println("call AppConfig.orderService");
          return new OrderServiceImpl(
              memberRepository(),
              discountPolicy());
      }

      @Bean
      public MemberRepository memberRepository() {
          //2번? 3번?
          System.out.println("call AppConfig.memberRepository");
          return new MemoryMemberRepository();
      }

      ...
}

```

`AppConfig` 클래스의 각 메서드가 호출될 때마다 로그를 출력한다. 그러면 `memberRepository()` 메서드는 총 세 번 호출되어야 합니다. 하지만 실제로 실행해보면, 각 메서드는 단 한 번씩만 호출된다.

```java
call AppConfig.memberService
call AppConfig.memberRepository
call AppConfig.orderService
```

이는 스프링이 `@Bean`이 붙은 메서드를 호출하여 객체를 생성하고, 그 생성된 객체를 스프링 컨테이너에 등록하는 과정에서 이미 생성된 `Bean`은 다시 생성하지 않고 동일한 `Bean`을 재사용함을 나타낸다. 즉, `AppConfig` 클래스의 각 메서드는 실제로 한 번만 호출되며, 이후에는 동일한 객체를 반환합니다. 이러한 방식으로 스프링은 싱글톤 패턴을 유지한다.

## **스프링의 @Configuration과 바이트코드 조작의 신비**

스프링 컨테이너는 '싱글톤 레지스트리'로, 스프링 빈이 항상 싱글톤이 되도록 보장하는 역할을 한다. 하지만 이게 어떻게 가능한 것일까요? 스프링은 바이트코드 조작 라이브러리를 사용해서 이를 가능하게 한다. 이 과정에서 중요한 역할을 하는 것이 @Configuration 어노테이션이 적용된 AppConfig 클래스이다.

예를 들어, 아래 코드를 살펴보자.

```java
@Test
  void configurationDeep() {
      ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

		//AppConfig도 스프링 빈으로 등록된다.
		AppConfig bean = ac.getBean(AppConfig.class);

		System.out.println("bean = " + bean.getClass());
		//출력: bean = class hello.core.AppConfig$$EnhancerBySpringCGLIB$$bd479d70
}

```

이 코드에서 AnnotationConfigApplicationContext에 넘겨진 AppConfig 클래스가 스프링 빈으로 등록되며, 조회해서 클래스 정보를 출력하면 다음과 같은 결과를 볼 수 있다.

```java
bean = class hello.core.AppConfig$$EnhancerBySpringCGLIB$$bd479d70
```

원래 순수한 클래스는 `class hello.core.AppConfig`와 같은 형태로 출력되어야 한다. 그러나 출력된 클래스 이름에 'CGLIB'라는 부분이 추가되어 있다. 이것은 스프링이 CGLIB 바이트코드 조작 라이브러리를 이용하여 AppConfig 클래스를 상속받은 새로운 클래스를 만들어낸 결과로, 이렇게 만들어진 새로운 클래스가 싱글톤이 보장되도록 동작한다.

이렇게 만들어진 클래스(AppConfig@CGLIB)는 아마도 다음과 같이 동작하게 될 것이다.

```java
@Bean
public MemberRepository memberRepository() {

	if (memoryMemberRepository가 이미 스프링 컨테이너에 등록되어 있으면?) {
		return 스프링 컨테이너에서 찾아서 반환;
	} else { //스프링 컨테이너에 없으면
		기존 로직을 호출해서 MemoryMemberRepository를 생성하고 스프링 컨테이너에 등록
		return 반환
	}
}

```

즉, @Bean이 붙은 메서드마다 해당 스프링 빈이 존재하는지 확인하고, 없으면 생성해서 스프링 컨테이너에 등록하며, 이미 존재하면 존재하는 빈을 반환하는 코드가 동적으로 만들어진다. 이러한 과정을 통해 싱글톤이 보장된다.

만약 @Configuration을 사용하지 않고 @Bean만 사용하면 어떻게 될까?

```java
bean = class hello.core.AppConfig
```

위의 출력 결과는 AppConfig가 CGLIB 기술 없이 순수한 AppConfig로 스프링 빈에 등록되었다는 것을 보여준다. 그 결과 MemberRepository는 총 3번 호출되는 상황이 발생한다. 이로 인해 기대했던 싱글톤이 보장되지 않게 된다! 

## 스프링 설정 정보는 항상 @Configuration을 사용하자! 
스프링에선 설정 정보를 @Configuration과 @Bean만 사용해도 스프링 빈이 제대로 등록되지만, 싱글톤을 보장하지 않는다. 그래서 스프링 컨테이너가 싱글톤을 보장하는 설정 파일에는 항상 @Configuration을 사용하자.
