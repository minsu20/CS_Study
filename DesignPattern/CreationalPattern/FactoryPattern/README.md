# 팩토리 패턴(Factory Pattern)


### **1. 팩토리 메서드 패턴 (Factory Method Pattern)**

- **목적**: 객체 생성의 책임을 서브 클래스에 위임한다. 이를 통해 클라이언트는 구체적인 클래스를 알 필요 없이 객체를 생성할 수 있다.
- **구성**:
    - **Creator**: 객체를 생성하는 추상 클래스나 인터페이스로, 팩토리 메서드를 정의한다.
    - **ConcreteCreator**: Creator 클래스를 상속받아 팩토리 메서드를 구현하는 클래스이다.
    - **Product**: 생성될 객체의 추상 클래스나 인터페이스이다.
    - **ConcreteProduct**: Product 클래스를 상속받아 구체적인 객체를 생성하는 클래스이다.

![image](https://github.com/minsu20/CS_Study/assets/86006389/cd4699d7-5e87-4456-85e5-72f2ff500f44)

**장점:**

1. **유연성**: 객체 생성 코드와 사용 코드를 분리하여 시스템의 유연성을 향상시킨다.
2. **확장성**: 새로운 제품 클래스를 쉽게 추가할 수 있다.

**단점:**

1. **클래스 수 증가**: 각 제품마다 팩토리 클래스가 필요하여 클래스 수가 늘어날 수 있다.


**예시**
![image](https://github.com/minsu20/CS_Study/assets/86006389/eac383c2-13a0-49c7-885e-d8c8c367a745)

### **2. 추상 팩토리 패턴 (Abstract Factory Pattern)**

- **목적**: 관련된 객체의 집합을 생성하기 위한 인터페이스를 제공한다. 이를 통해 시스템이 새로운 제품 집합을 쉽게 통합하거나 교체할 수 있도록 한다.
- **구성**:
    - **AbstractFactory**: 여러 팩토리 메서드를 포함하는 추상 클래스나 인터페이스이다. 각 메서드는 다른 종류의 객체를 생성한다.
    - **ConcreteFactory**: AbstractFactory 클래스를 상속받아 구체적인 객체 집합을 생성하는 클래스이다.
    - **AbstractProduct**: 생성될 객체의 추상 클래스나 인터페이스이다.
    - **ConcreteProduct**: AbstractProduct 클래스를 상속받아 구체적인 객체를 생성하는 클래스이다.

![image](https://github.com/minsu20/CS_Study/assets/86006389/dd4f174b-2c41-487a-b96a-bcbfc30090e9)

**장점:**

1. **유연성**: 제품의 구체적인 클래스를 클라이언트 코드에서 분리한다.
2. **일관성**: 한 팩토리에서 생성된 제품들은 잘 동작하도록 설계되었다.

**단점:**

1. **클래스 수 증가**: 여러 제품군과 제품에 대한 클래스가 필요하여 클래스 수가 늘어날 수 있다.
2. **제품 변경의 어려움**: 기존 제품군에 제품을 추가하거나 변경할 때 모든 팩토리 클래스를 수정해야 할 수도 있다.

**예시**
![image](https://github.com/minsu20/CS_Study/assets/86006389/c354fa7e-ab7a-41b9-b692-e4cf931cb877)

### **차이점:**

- **팩토리 메서드**는 단일 메서드를 통해 하나의 객체를 생성하는 반면, **추상 팩토리**는 관련된 객체의 집합을 생성하는 여러 메서드를 제공한다.
- **팩토리 메서드**는 객체 생성을 서브 클래스에 위임하는 반면, **추상 팩토리**는 객체 생성을 관련된 객체 집합에 위임한다.
- **추상 팩토리**는 시스템이 새로운 제품 집합을 쉽게 통합하거나 교체할 수 있도록 설계된다.

<br>

## Factory Bean

Spring에서는 **`FactoryBean`** 이라는 인터페이스를 제공하는데, 이 인터페이스는 객체를 생성하는 팩토리 역할을 한다. **`FactoryBean`** 을 구현하는 빈을 등록하면, 이 빈은 직접적으로 노출되는 것이 아니라 **`FactoryBean`** 이 생성하는 객체가 노출되는 팩토리 패턴의 형태를 보여준다.


```java
public class Tool {
    private int id;

    public Tool(int id) {
        this.id = id;
    }

    // getters and setters...
}

public class ToolFactory implements FactoryBean<Tool> {
    private int factoryId;
    private int toolId;

    @Override
    public Tool getObject() throws Exception {
        return new Tool(toolId);
    }

    @Override
    public Class<?> getObjectType() {
        return Tool.class;
    }

    // getters and setters for factoryId and toolId...
}

@Configuration
public class AppConfig {
    @Bean
    public ToolFactory toolFactory() {
        ToolFactory factory = new ToolFactory();
        factory.setFactoryId(8080);
        factory.setToolId(7070);
        return factory;
    }

    @Bean
    @Scope("prototype")
    public Tool tool() throws Exception {
        return toolFactory().getObject();
    }
}

```

`FactoryBean`을 사용하는 이유는 객체 생성의 로직을 캡슐화하고, 더 복잡한 생성 과정을 처리하거나, 더 높은 수준의 프로그래밍 구조를 가능하게 하기 위함이다.

1. **복잡한 객체 생성**: `new` 키워드를 사용하면 매우 간단한 객체를 쉽게 생성할 수 있다. 그러나, 생성자의 매개변수가 많거나, 객체의 생성 후 초기화 과정이 필요하거나, 객체 생성에 사전 조건이 필요한 경우에는 `new` 키워드만으로는 코드가 복잡해질 수 있다. 이러한 경우 `FactoryBean`을 사용하여 객체 생성 로직을 캡슐화함으로써 코드의 가독성을 높일 수 있다.
2. **Spring 컨테이너의 생명주기 관리**: `FactoryBean`을 사용하면 Spring 컨테이너가 해당 객체의 생명주기를 관리하게 된다. 이는 객체의 생성과 소멸, 그리고 의존성 주입 등의 관리를 Spring에게 맡길 수 있다는 것을 의미합니다. 이로 인해 개발자는 비즈니스 로직에 더 집중할 수 있게 된다.
3. **싱글톤 또는 프로토타입 스코프 관리**: `FactoryBean`을 사용하면 Spring에서 제공하는 스코프를 쉽게 관리할 수 있다. 예를 들어, 싱글톤 스코프를 사용하여 같은 타입의 Bean을 요청할 때마다 동일한 객체를 반환하도록 하거나, 프로토타입 스코프를 사용하여 요청할 때마다 새로운 객체를 생성하도록 할 수 있다.  

<br>

> ❗ 주어진 예시에서, `ToolFactory`는 `Tool` 객체의 생성 로직을 캡슐화하고 있다. `Tool` 객체가 생성될 때마다 `Tool` 객체에 `id`를 설정하는 등의 초기화 과정이 필요하므로, 이러한 과정을 `ToolFactory`에서 관리하도록 함으로써 코드의 가독성과 재사용성을 높일 수 있다. 또한, `FactoryBean`을 사용함으로써 Spring 컨테이너가 `Tool` 객체의 생명주기를 관리하게 되므로, 개발자는 `Tool` 객체의 생성과 소멸에 대해 신경 쓸 필요가 없다.
