# 팩토리 패턴(Factory Pattern)


## **팩토리 패턴의 정의**

팩토리 패턴은 객체를 생성하는 방법을 캡슐화하는 디자인 패턴이다. 이 패턴은 객체를 생성하는 코드와 이를 사용하는 코드를 분리하여, 높은 유연성과 확장성을 제공한다. 클래스의 인스턴스를 생성하는 인터페이스를 제공하여 어떤 클래스의 인스턴스를 만들지는 서브클래스가 결정하도록 한다. 이를 통해 코드는 특정 클래스의 인스턴스를 생성하는 대신에 인터페이스를 통해 생성된 객체를 사용하게 된다.

```java
javaCopy code
public interface Animal {
    void speak();
}

public class Dog implements Animal {
    @Override
    public void speak() {
        System.out.println("Woof!");
    }
}

public class Cat implements Animal {
    @Override
    public void speak() {
        System.out.println("Meow!");
    }
}

public class AnimalFactory {
    public Animal createAnimal(String type) {
        if ("dog".equals(type)) {
            return new Dog();
        } else if ("cat".equals(type)) {
            return new Cat();
        }
        return null;
    }
}

public class Main {
    public static void main(String[] args) {
        AnimalFactory animalFactory = new AnimalFactory();

        Animal dog = animalFactory.createAnimal("dog");
        dog.speak();  // Outputs "Woof!"

        Animal cat = animalFactory.createAnimal("cat");
        cat.speak();  // Outputs "Meow!"
    }
}

```

위의 코드에서 **`AnimalFactory`** 는 팩토리 클래스로, 특정 타입의 **`Animal`** 객체를 생성하는 **`createAnimal()`** 메서드를 제공합니다. 이 메서드를 통해 **`Dog`** 와 **`Cat`** 객체를 생성하고 있다.

<br>

### **팩토리 패턴의 장점**

1. **유연성**: 팩토리 패턴을 사용하면, 특정 타입의 객체를 생성하는 로직을 한 곳에서 관리할 수 있습니다. 이는 코드의 유연성을 높이며, 코드 수정이 필요할 때 중복을 줄일 수 있자.
2. **확장성**: 새로운 타입의 객체를 추가해야 할 경우, 팩토리 클래스만 수정하면 된다. 이는 코드의 확장성을 높여준다.
3. 유지보수성 증가: 객체 생성과 비즈니스 로직을 분리함으로써 코드의 의존성을 줄일 수 있고 이로 인해 유지 보수성이 증가한다.

### **팩토리 패턴의 단점**

1. **클래스 수 증가**: 각각의 제품에 대한 클래스와 이를 생성하는 팩토리 클래스를 모두 만들어야 하므로, 클래스의 수가 증가합니다.
2. **복잡성 증가**: 많은 수의 관련 클래스가 생기면서 코드의 복잡성이 증가할 수 있습니다.

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
