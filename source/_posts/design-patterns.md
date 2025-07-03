title: 设计模式总结（所有代码均通过与 chatGPT 调试获取）
author: Laiyong Wang
tags:
  - 设计模式
categories: []
date: 2023-05-09 09:47:00
---
### 开闭原则
对于扩展是开放的，对于修改是封闭的

问个问题：
在一个类里面新增一个方法，符合开闭原则么？
### 创建型模式
#### 单例模式
三私一公
私有化静态属性,私有化构造方法,私有化克隆方法,公有化静态方法。 单例模式:即一个类只会被实例化一次，无论在任何地方调用多少次
```
class Singleton {
    private static $instance;

    // 私有构造函数，防止类被实例化
    private function __construct() {
    }

    // 获取实例的静态方法
    public static function getInstance() {
        if (!self::$instance) {
            self::$instance = new self();
        }
        return self::$instance;
    }

    // 克隆方法声明为公共的
    public function __clone() {
        throw new \Exception("Cloning of singleton instance is not allowed.");
    }
}

// 使用单例模式获取实例
$singleton = Singleton::getInstance();

// 尝试克隆单例实例
$clone = clone $singleton; // 会抛出异常
```
#### 工厂模式
##### 简单工厂模式
简单工厂模式（Simple Factory Pattern）是一种创建型设计模式，它提供了一个统一的接口来创建对象，而不需要暴露对象的创建逻辑给客户端。简单工厂模式通过一个工厂类来封装对象的创建过程，并根据客户端的请求返回相应的对象实例。

简单工厂模式涉及以下几个角色：

- 工厂类（Factory Class）：负责创建对象的工厂类，提供一个静态方法或实例方法用于创建对象。
- 产品接口（Product Interface）：定义了产品对象的接口，客户端通过该接口与产品对象进行交互。
- 具体产品类（Concrete Product Class）：实现了产品接口，是工厂类创建的具体产品。

以下是一个简单的简单工厂模式的示例代码：
```
// 产品接口
interface Product {
    public function operation();
}

// 具体产品类A
class ConcreteProductA implements Product {
    public function operation() {
        echo "ConcreteProductA operation\n";
    }
}

// 具体产品类B
class ConcreteProductB implements Product {
    public function operation() {
        echo "ConcreteProductB operation\n";
    }
}

// 工厂类
class Factory {
    public static function createProduct($type) {
        if ($type == 'A') {
            return new ConcreteProductA();
        } elseif ($type == 'B') {
            return new ConcreteProductB();
        }
        return null;
    }
}

// 客户端代码
$productA = Factory::createProduct('A');
$productA->operation();

$productB = Factory::createProduct('B');
$productB->operation();

```
在上述代码中，我们定义了一个产品接口 `Product`，具体产品类 `ConcreteProductA` 和 `ConcreteProductB` 分别实现了该接口。工厂类 `Factory` 提供了一个静态方法 `createProduct()`，根据客户端传入的参数来创建具体的产品对象。

客户端通过调用工厂类的 `createProduct()` 方法来获取产品对象，然后通过产品对象的接口方法来执行相应的操作。

简单工厂模式适用于以下场景：

1. 当需要创建的对象较少且相对简单时，可以使用简单工厂模式。
2. 当对象的创建逻辑相对固定，不会频繁变动时，简单工厂模式比较适合。
3. 当客户端不需要关心对象的创建细节，只需要获取到对象进行操作时，简单工厂模式能够提供统一的接口来创建对象。
需要注意的是，简单工厂模式违背了开闭原则，因为每次添加新的具体产品类时，都需要修改工厂类的代码。因此，当产品类的种类较多且经常变动时，考虑使用其他创建型设计模式，如工厂方法模式或抽象工厂模式，来更好地满足开闭原则的要求。

##### 工厂方法模式

工厂方法模式（Factory Method Pattern）是一种创建型设计模式，它提供了一种将对象的实例化延迟到子类的方法。工厂方法模式通过定义一个创建对象的工厂方法，让子类决定具体要实例化的对象类型。

工厂方法模式涉及以下几个角色：

- 抽象产品（Abstract Product）：定义了产品对象的接口，客户端通过该接口与产品对象进行交互。
- 具体产品（Concrete Product）：实现了抽象产品接口，是工厂方法模式所创建的具体产品。
- 抽象工厂（Abstract Factory）：定义了创建产品的工厂方法，可以是接口或抽象类，也可以提供一些默认实现。
- 具体工厂（Concrete Factory）：实现了抽象工厂接口，负责创建具体的产品对象。

以下是一个简单的工厂方法模式的示例代码：
```
// 抽象产品
interface Product {
    public function operation();
}

// 具体产品A
class ConcreteProductA implements Product {
    public function operation() {
        echo "ConcreteProductA operation\n";
    }
}

// 具体产品B
class ConcreteProductB implements Product {
    public function operation() {
        echo "ConcreteProductB operation\n";
    }
}

// 抽象工厂
interface Factory {
    public function createProduct();
}

// 具体工厂A
class ConcreteFactoryA implements Factory {
    public function createProduct() {
        return new ConcreteProductA();
    }
}

// 具体工厂B
class ConcreteFactoryB implements Factory {
    public function createProduct() {
        return new ConcreteProductB();
    }
}

// 客户端代码
$factoryA = new ConcreteFactoryA();
$productA = $factoryA->createProduct();
$productA->operation();

$factoryB = new ConcreteFactoryB();
$productB = $factoryB->createProduct();
$productB->operation();
```
在上述代码中，我们定义了一个抽象产品接口 `Product`，具体产品类` ConcreteProductA` 和 `ConcreteProductB` 实现了该接口。抽象工厂接口 `Factory` 定义了一个工厂方法 `createProduct()`，具体工厂类 `ConcreteFactoryA` 和 `ConcreteFactoryB` 实现了该接口。

客户端代码根据需要创建具体的工厂对象，然后通过工厂对象调用 `createProduct()` 方法来创建具体的产品对象。客户端只需和抽象产品接口进行交互，无需关心具体的产品和工厂类。

工厂方法模式通过将对象的创建交给子类来实现，提供了一种灵活的扩展方式。每个具体产品对应一个具体工厂，当需要添加新的产品时，只需创建对应的具体产品类和具体工厂类，无需修改现有的代码。这符合开闭原则，同时也增加了代码的可维护性和扩展性。

工厂方法模式适用于以下场景：

- 当需要创建的对象属于某个产品族而不是单个产品时，可以使用工厂方法模式。工厂方法模式可以让每个具体工厂负责创建特定的产品族，从而将产品的创建逻辑进行解耦。
- 当客户端不关心具体的产品类，而只关心与产品接口进行交互时，可以使用工厂方法模式。工厂方法模式将具体产品的创建过程封装在具体工厂中，客户端只需要通过工厂接口与产品进行交互，无需知道具体的产品细节。
- 当需要添加新的产品类型时，可以通过创建对应的具体产品类和具体工厂类来扩展系统，而无需修改现有的代码。工厂方法模式具有良好的扩展性，符合开闭原则。
需要注意的是，工厂方法模式在增加新的产品时需要同时创建具体产品类和具体工厂类，这会增加一定的代码复杂性。如果系统中只有少量的产品类型，并且不太频繁地添加新的产品，可以考虑使用简单工厂模式来简化代码。如果系统中存在多个产品族，并且需要动态切换产品族，可以考虑使用抽象工厂模式来实现更高层次的抽象和灵活性。根据具体的需求和设计考虑，选择适合的创建型设计模式。

##### 抽象工厂模式

抽象工厂模式（Abstract Factory Pattern）是一种创建型设计模式，它提供了一种方式来创建一组相关或依赖的对象，而不需要指定具体的类。抽象工厂模式通过引入抽象工厂和具体工厂的概念，将产品的创建和使用解耦，使得客户端代码更加灵活、可扩展和可维护。

抽象工厂模式的主要角色包括：

- 抽象工厂（Abstract Factory）：定义了一组用于创建产品的抽象方法，每个方法对应一个产品族的创建。
- 具体工厂（Concrete Factory）：实现了抽象工厂接口，负责实际创建具体产品的对象。
- 抽象产品（Abstract Product）：定义了产品对象的共同接口，描述了产品的特性和行为。
- 具体产品（Concrete Product）：实现了抽象产品接口，是由具体工厂创建的实际产品。

以下是一个简单的抽象工厂模式的示例代码：
```
// 抽象产品接口
// 抽象产品A接口
interface AbstractProductA {
    public function operationA(): string;
}

// 具体产品A1
class ConcreteProductA1 implements AbstractProductA {
    public function operationA(): string {
        return "ConcreteProductA1 operationA";
    }
}

// 具体产品A2
class ConcreteProductA2 implements AbstractProductA {
    public function operationA(): string {
        return "ConcreteProductA2 operationA";
    }
}

// 抽象产品B接口
interface AbstractProductB {
    public function operationB(): string;
}

// 具体产品B1
class ConcreteProductB1 implements AbstractProductB {
    public function operationB(): string {
        return "ConcreteProductB1 operationB";
    }
}

// 具体产品B2
class ConcreteProductB2 implements AbstractProductB {
    public function operationB(): string {
        return "ConcreteProductB2 operationB";
    }
}

// 抽象工厂接口
interface AbstractFactory {
    public function createProductA(): AbstractProductA;
    public function createProductB(): AbstractProductB;
}

// 具体工厂1
class ConcreteFactory1 implements AbstractFactory {
    public function createProductA(): AbstractProductA {
        return new ConcreteProductA1();
    }

    public function createProductB(): AbstractProductB {
        return new ConcreteProductB1();
    }
}

// 具体工厂2
class ConcreteFactory2 implements AbstractFactory {
    public function createProductA(): AbstractProductA {
        return new ConcreteProductA2();
    }

    public function createProductB(): AbstractProductB {
        return new ConcreteProductB2();
    }
}

// 客户端代码
$factory1 = new ConcreteFactory1();
$productA1 = $factory1->createProductA();
$productB1 = $factory1->createProductB();

echo $productA1->operationA();  // 输出：ConcreteProductA1 operationA
echo $productB1->operationB();  // 输出：ConcreteProductB1 operationB

$factory2 = new ConcreteFactory2();
$productA2 = $factory2->createProductA();
$productB2 = $factory2->createProductB();

echo $productA2->operationA();  // 输出：ConcreteProductA2 operationA
echo $productB2->operationB();  // 输出：ConcreteProductB2 operationB
```

在上述示例中，我们首先定义了抽象产品A和抽象产品B的接口 AbstractProductA 和 AbstractProductB，然后实现了具体的产品类 `ConcreteProductA1`、`ConcreteProductA2`、`ConcreteProductB1` 和 `ConcreteProductB2`，它们分别实现了对应的抽象产品接口。

接下来，我们定义了抽象工厂接口 `AbstractFactory`，其中包含了创建产品A和产品B的方法。然后，我们实现了具体的工厂类 `ConcreteFactory1` 和 `ConcreteFactory2`，它们分别实现了抽象工厂接口，负责创建具体的产品对象。

最后，我们在客户端代码中使用具体的工厂实例（`$factory1` 和 `$factory2`）创建产品A和产品B的实例，并调用它们的操作方法进行

使用抽象工厂模式的主要优点包括：
- 将产品的创建和使用解耦，客户端代码与具体产品类解耦，使得客户端代码更具灵活性和可维护性。
- 可以确保创建的产品对象属于同一产品族，保证产品之间的兼容性。
- 新增具体工厂和产品类容易，符合开闭原则。

抽象工厂模式适用于以下场景：

- 系统需要一次性创建一整套相关的产品对象，而这些产品对象之间存在关联或依赖关系。
- 需要提供一组可替换的产品族，以适应不同的需求。
- 客户端不应该依赖于具体产品类，而是通过抽象接口与产品交互。

总结来说，抽象工厂模式提供了一种创建相关产品族的方式，使得客户端代码与具体产品类解耦，同时保持了产品之间的一致性。这种模式适用于需要一次性创建一系列相关产品对象，且希望这些产品对象之间保持关联和兼容性的场景。
##### 三种工厂模式的选择
简单工厂模式：生产同一工厂中的固定产品

工厂方法模式：生产不同工厂中的同一产品族的任意产品

抽象工厂模式：用来不同工厂中的任意产品族的任意产品
#### 建造者模式
建造者模式（Builder Pattern）是一种创建对象的设计模式，它通过将对象的构建过程与其表示分离，使得同样的构建过程可以创建不同的表示。

建造者模式通常适用于创建复杂对象，该对象由多个部分组成，并且这些部分的创建顺序或配置选项可能不同。通过使用建造者模式，我们可以将对象的构建过程分解为一系列步骤，并且可以根据需要定制每个步骤的实现细节，从而创建具有不同属性的对象
```
// 产品类
class Product {
    private $part1;
    private $part2;
    private $part3;

    public function setPart1($part1) {
        $this->part1 = $part1;
    }

    public function setPart2($part2) {
        $this->part2 = $part2;
    }

    public function setPart3($part3) {
        $this->part3 = $part3;
    }

    public function show() {
        echo "Part1: " . $this->part1 . "\n";
        echo "Part2: " . $this->part2 . "\n";
        echo "Part3: " . $this->part3 . "\n";
    }
}

// 抽象建造者类
abstract class Builder {
    protected $product;

    public function __construct() {
        $this->product = new Product();
    }

    public abstract function buildPart1();
    public abstract function buildPart2();
    public abstract function buildPart3();

    public function getProduct() {
        return $this->product;
    }
}

// 具体建造者类A
class ConcreteBuilderA extends Builder {
    public function buildPart1() {
        $this->product->setPart1("Part1A");
    }

    public function buildPart2() {
        $this->product->setPart2("Part2A");
    }

    public function buildPart3() {
        $this->product->setPart3("Part3A");
    }
}

// 具体建造者类B
class ConcreteBuilderB extends Builder {
    public function buildPart1() {
        $this->product->setPart1("Part1B");
    }

    public function buildPart2() {
        $this->product->setPart2("Part2B");
    }

    public function buildPart3() {
        $this->product->setPart3("Part3B");
    }
}

// 导演类
class Director {
    public function construct(Builder $builder) {
        $builder->buildPart1();
        $builder->buildPart2();
        $builder->buildPart3();
    }
}

// 使用建造者模式创建产品
$builderA = new ConcreteBuilderA();
$director = new Director();
$director->construct($builderA);
$productA = $builderA->getProduct();
$productA->show();
// 输出：
// Part1: Part1A
// Part2: Part2A
// Part3: Part3A

$builderB = new ConcreteBuilderB();
$director->construct($builderB);
$productB = $builderB->getProduct();
$productB->show();
// 输出：
// Part1: Part1B
// Part2: Part2B
// Part3: Part3B
```
在上述代码中，我们有一个产品类 `Product`，它包含了三个部分：`part1` 、`part2` 和 `part3`，并提供了设置和展示这些部分的方法。

抽象建造者类 `Builder` 定义了创建产品的步骤，并包含一个返回最终产品的方法 `getProduct()`。具体的建造者类 `ConcreteBuilderA` 和 `ConcreteBuilderB` 继承自抽象建造者类，并实现了构建每个部分的具体方法。

导演类 `Director` 负责根据给定的建造者实例，按照一定的顺序调用建造者的方法来构建产品。

在客户端中，我们创建了一个具体的建造者实例 `ConcreteBuilderA` 和 `ConcreteBuilderB`，然后通过导演类 `Director` 来构建产品。最终，我们通过建造者实例的 `getProduct()` 方法获取构建完成的产品，并调用产品的 `show()` 方法展示产品的各个部分。

通过使用建造者模式，我们可以灵活地创建具有不同部分组合的产品。每个具体的建造者类负责创建特定的产品类型，而导演类则负责控制建造的过程。这样，我们可以通过改变具体的建造者类或调整导演类的调用顺序，以获得不同的产品配置。建造者模式将复杂对象的构建过程封装起来，使得客户端代码与产品的创建过程解耦，提高了代码的可维护性和扩展性。
#### 原型模式
原型模式（Prototype Pattern）是一种创建型设计模式，它允许通过克隆（复制）现有对象来创建新对象，而无需依赖于具体类的构造函数。通过原型模式，我们可以避免重复创建对象，提高性能，并且能够动态添加或修改对象的属性。

在原型模式中，我们定义一个抽象原型类（或接口），其中包含一个用于克隆对象的方法 clone()。具体的原型类实现该抽象方法，并进行对象的克隆操作
```
// 抽象原型类
abstract class Prototype {
    protected $name;

    public function getName() {
        return $this->name;
    }

    public function setName($name) {
        $this->name = $name;
    }

    // 克隆方法
    abstract public function clone();
}

// 具体原型类
class ConcretePrototype extends Prototype {
    public function clone() {
        $clone = new ConcretePrototype();
        $clone->setName($this->name);
        return $clone;
    }
}

// 使用原型模式创建对象
$prototype = new ConcretePrototype();
$prototype->setName("Prototype 1");

// 克隆对象
$clone = $prototype->clone();
$clone->setName("Prototype 2");

echo $prototype->getName(); // 输出: Prototype 1
echo $clone->getName(); // 输出: Prototype 2
```
在上述代码中，我们定义了一个抽象原型类 `Prototype`，其中包含一个属性 `name` 和克隆方法 `clone()`。具体原型类 `ConcretePrototype` 继承自抽象原型类，并实现了克隆方法，通过创建一个新的对象并设置相同的属性值来完成克隆操作。

在客户端中，我们创建了一个原型对象 `prototype`，并设置其属性 `name` 为 `Prototype 1`。然后通过调用 `clone()` 方法来克隆该对象，并对克隆对象的属性 `name` 进行修改。

通过原型模式，我们可以通过克隆现有对象来创建新对象，而无需重复进行对象的实例化和初始化操作。这样可以提高性能，并且能够动态添加或修改对象的属性，使得对象的创建更加灵活和可扩展。

#### 单例模式、工厂模式、建造者模式、原型模式之间的区别和如何选用

1. 单例模式：
目的：确保类只有一个实例，并提供全局访问点。
使用场景：当需要保证系统中只有一个实例存在，并且该实例需要在整个系统中被访问时，可以使用单例模式。常见的应用场景包括日志记录器、数据库连接池等。

2. 工厂模式：
目的：将对象的创建过程封装在工厂类中，通过工厂类来创建对象。
使用场景：当需要根据不同的条件创建具有共同接口的多个对象时，可以使用工厂模式。工厂模式将对象的实例化过程封装在工厂类中，客户端只需要与工厂类进行交互，而无需直接依赖具体的对象类。常见的应用场景包括创建不同类型的文件解析器、创建不同类型的数据库访问对象等。

3. 建造者模式：
目的：将对象的构建过程与其表示分离，使得同样的构建过程可以创建不同的表示。
使用场景：当需要创建复杂对象，并且对象的构建过程具有多个步骤或配置选项时，可以使用建造者模式。建造者模式将对象的构建过程分解为多个步骤，并允许客户端按照需要定制每个步骤的实现细节，以创建具有不同属性的对象。常见的应用场景包括创建具有多个部件的汽车、创建具有多种配置的电子设备等。

4. 原型模式：
目的：通过克隆（复制）现有对象来创建新对象，而无需依赖于具体类的构造函数。
使用场景：当需要根据现有对象创建新对象，并且创建过程较为复杂时，可以使用原型模式。原型模式允许通过克隆现有对象来创建新对象，从而避免了重复创建对象的开销，并能够动态添加或修改对象的属性。常见的应用场景包括创建大量相似对象的场景，如在图形处理中复制图形对象、在数据库查询中复制查询结果等。
在选择使用哪种设计模式时，需要根据具体的需求和情况进行评估：

如果需要确保系统中只有一个实例存在，并且需要全局访问该实例，可以选择单例模式。
如果需要根据不同条件创建具有共同接口的对象，可以选择工厂模式。
如果需要创建复杂对象，并且对象的构建过程具有多个步骤或配置选项，可以选择建造者模式。
如果需要根据现有对象创建新对象，并且创建过程较为复杂，可以选择原型模式。

另外，也可以根据设计模式的特点和优缺点来做选择：
单例模式适用于需要确保全局只有一个实例存在的场景，但可能会对代码的测试和扩展性带来一定困难。
工厂模式适用于对象的创建需要封装在工厂类中，并且客户端不需要了解具体的对象创建过程。
建造者模式适用于创建复杂对象的场景，可以按照需要定制对象的构建过程，但可能会增加代码的复杂性。
原型模式适用于需要根据现有对象创建新对象的场景，并且创建过程较为复杂，但可能会增加对象的内存消耗。
选择适当的设计模式要根据具体的需求和场景进行评估，并考虑其适用性、灵活性、可维护性以及对系统的影响。有时候，也可以结合多种设计模式来解决复杂的问题，以满足系统的需求。

### 结构型模式
#### 代理模式
代理模式（Proxy Pattern）是一种结构型设计模式，它允许通过创建一个代理对象来控制对另一个对象的访问。代理对象与原始对象实现相同的接口，客户端通过代理对象来访问原始对象，从而可以在访问过程中加入额外的逻辑。

代理模式的主要目的是在不改变原始对象的情况下，通过引入代理对象来控制对原始对象的访问。代理对象可以在调用原始对象的方法之前或之后执行额外的操作，例如权限验证、缓存、延迟加载等。

代理模式涉及以下几个角色：

1. 抽象主题（Subject）：定义了代理对象和原始对象共同实现的接口或抽象类。
2. 真实主题（Real Subject）：实现了抽象主题接口，是代理模式中的原始对象。
3. 代理（Proxy）：实现了抽象主题接口，持有对真实主题的引用，在需要时将请求传递给真实主题，并可以在调用前后执行额外的操作。

以下是一个代理模式的示例代码：
```
// 抽象主题接口
interface Subject {
    public function request();
}

// 真实主题类
class RealSubject implements Subject {
    public function request() {
        echo "RealSubject: Handling request.\n";
    }
}

// 代理类
class Proxy implements Subject {
    private $realSubject;

    public function request() {
        if (!$this->realSubject) {
            $this->realSubject = new RealSubject();
        }

        $this->preRequest();
        $this->realSubject->request();
        $this->postRequest();
    }

    public function preRequest() {
        echo "Proxy: Preparing request.\n";
    }

    public function postRequest() {
        echo "Proxy: Finalizing request.\n";
    }
}

// 客户端代码
$proxy = new Proxy();
$proxy->request();
```
在上述代码中，我们有一个抽象主题接口 `Subject`，定义了代理类和真实主题类共同实现的方法 `request()`。真实主题类 `RealSubject` 实现了抽象主题接口，是代理模式中的原始对象。代理类 Proxy 也实现了抽象主题接口，它持有对真实主题对象的引用，并在需要时将请求传递给真实主题对象。代理类可以在调用前后执行额外的操作。

在客户端代码中，我们创建了一个代理对象 `Proxy`，并通过调用 `request()` 方法来访问原始对象。在访问过程中，代理类执行了预处理和后处理的操作，并将请求传递给真实主题对象进行处理。

代理模式可以提供一种间接访问对象的方式，并允许在访问过程中增加额外的逻辑。这样可以实现对原始对象的保护、
性能优化、访问控制等。代理模式的选择主要取决于以下考虑因素：

1. 保护和访问控制：如果需要对原始对象进行保护，只允许特定条件下的访问，可以使用代理模式来控制对原始对象的访问权限。

2. 延迟加载：如果创建和初始化原始对象的成本较高，而且可能不一定会被使用到，可以使用代理模式来实现延迟加载，即在需要时才真正创建和初始化原始对象。

3. 缓存：如果需要对频繁访问的数据进行缓存，可以使用代理模式来在访问前先检查缓存，并返回缓存数据，从而提高系统性能。

4. 日志记录和审计：如果需要对原始对象的访问进行日志记录或审计，可以使用代理模式来在访问前后记录相关信息。

5. 远程访问：如果需要在客户端和服务端之间进行远程通信，可以使用代理模式来封装网络通信的细节，使得客户端可以通过代理对象访问远程服务。

在选择使用代理模式时，需要考虑具体的需求和场景，并根据代理模式的特点来决定是否适合使用。代理模式可以提供灵活性、可维护性和安全性，但可能会引入额外的复杂性和开销。因此，需要权衡利弊，选择适合的设计模式来解决问题。

#### 装饰器模式
装饰器模式（Decorator Pattern）是一种结构型设计模式，它允许在不改变原始对象的情况下，动态地扩展对象的功能。装饰器模式通过将对象包装在一个装饰器对象中，以提供额外的行为或责任。

装饰器模式的核心思想是通过组合而不是继承来实现功能的扩展。它允许在运行时动态地添加、删除或修改对象的行为，而无需修改原始对象的结构。这使得装饰器模式具有灵活性和可扩展性，同时遵循开闭原则。

装饰器模式涉及以下几个角色：

1. 抽象构件（Component）：定义了被装饰对象和装饰器对象的共同接口。
2. 具体构件（Concrete Component）：实现了抽象构件接口，是装饰器模式中的原始对象。
3. 抽象装饰器（Decorator）：实现了抽象构件接口，并持有一个抽象构件对象的引用。它可以通过对抽象构4. 件的包装来扩展其行为。

具体装饰器（Concrete Decorator）：扩展了抽象装饰器，并在被装饰对象的行为前后添加额外的功能。

以下是一个装饰器模式的示例代码：
```
// 抽象构件接口
interface Component {
    public function operation();
}

// 具体构件类
class ConcreteComponent implements Component {
    public function operation() {
        echo "ConcreteComponent: Operation.\n";
    }
}

// 抽象装饰器类
abstract class Decorator implements Component {
    protected $component;

    public function __construct(Component $component) {
        $this->component = $component;
    }

    public function operation() {
        $this->component->operation();
    }
}

// 具体装饰器类A
class ConcreteDecoratorA extends Decorator {
    public function operation() {
        parent::operation();
        $this->additionalOperation();
    }

    public function additionalOperation() {
        echo "ConcreteDecoratorA: Additional operation.\n";
    }
}

// 具体装饰器类B
class ConcreteDecoratorB extends Decorator {
    public function operation() {
        parent::operation();
        $this->additionalOperation();
    }

    public function additionalOperation() {
        echo "ConcreteDecoratorB: Additional operation.\n";
    }
}

// 客户端代码
$component = new ConcreteComponent();
$decoratorA = new ConcreteDecoratorA($component);
$decoratorB = new ConcreteDecoratorB($decoratorA);

$decoratorB->operation();
```
在上述代码中，我们有一个抽象构件接口 `Component`，定义了被装饰对象和装饰器对象共同实现的方法 `operation()`。具体构件类 `ConcreteComponent` 实现了抽象构件接口，是装饰器模式中的原始对象。抽象装饰器类 `Decorator` 是一个抽象类，实现了抽象构件接口并持有一个抽象构件对象的引用。它通过对抽象构件的包装来扩展其行为，并在 `operation()` 方法中调用原始对象的操作。

具体装饰器类 `ConcreteDecoratorA` 和 `ConcreteDecoratorB` 继承自抽象装饰器类，并在原始对象的操作前后添加了额外的功能。它们在 `operation()` 方法中先调用父类的 `operation()` 方法，然后执行自己的附加操作。

在客户端代码中，我们创建了一个具体构件对象 `ConcreteComponent`，然后通过创建具体装饰器对象 `ConcreteDecoratorA` 和 `ConcreteDecoratorB` 来装饰原始对象。通过嵌套装饰器对象，我们可以实现多层次的功能扩展。

装饰器模式的优点在于可以动态地为对象添加功能，避免了使用继承可能带来的类爆炸问题。它使得代码更加灵活可扩展，符合开闭原则。然而，装饰器模式也可能引入过多的小对象和复杂的层次结构，导致代码复杂性增加。

选择使用装饰器模式要根据具体的需求和场景进行评估。当需要在运行时动态地为对象添加功能，并且不希望改变原始对象的结构时，装饰器模式是一个很好的选择。
#### 外观模式/门面模式
外观模式（Facade Pattern）是一种结构型设计模式，旨在为客户端提供一个简化的接口，以便访问复杂子系统的功能。外观模式通过创建一个外观类（也称为门面类），该类包装了一组子系统的接口，并将其暴露给客户端。这样，客户端只需要与外观类交互，而不需要直接与子系统进行复杂的交互。

外观模式的核心思想是通过引入一个外观类来隐藏子系统的复杂性，提供一个简单、统一的接口给客户端使用。外观类知道如何与子系统进行交互，根据客户端的需求调用适当的子系统方法，然后将结果返回给客户端。这种封装和隐藏的方式简化了客户端的调用过程，并降低了客户端与子系统之间的耦合度。

外观模式涉及以下几个角色：

1. 外观类（Facade）：提供了简化的接口，封装了子系统的复杂功能，并将客户端的请求委派给适当的子系统对象处理。
2. 子系统类（Subsystem）：实现了子系统的功能，处理来自外观类的请求。

以下是一个简单的外观模式的示例代码：
```
// 子系统类
class SubsystemA {
    public function operationA() {
        echo "SubsystemA: Operation A\n";
    }
}

class SubsystemB {
    public function operationB() {
        echo "SubsystemB: Operation B\n";
    }
}

class SubsystemC {
    public function operationC() {
        echo "SubsystemC: Operation C\n";
    }
}

// 外观类
class Facade {
    private $subsystemA;
    private $subsystemB;
    private $subsystemC;

    public function __construct() {
        $this->subsystemA = new SubsystemA();
        $this->subsystemB = new SubsystemB();
        $this->subsystemC = new SubsystemC();
    }

    public function operation() {
        $this->subsystemA->operationA();
        $this->subsystemB->operationB();
        $this->subsystemC->operationC();
    }
}

// 客户端代码
$facade = new Facade();
$facade->operation();
```
在上述代码中，我们定义了三个子系统类 `SubsystemA` 、`SubsystemB` 和 `SubsystemC`，它们分别实现了不同的功能。外观类 `Facade` 封装了这些子系统的接口，并提供了一个简化的接口 `operation()`，用于执行一系列复杂的操作。

客户端只需要通过外观类进行调用，而不需要直接与子系统类进行交互。外观类在接收到请求后，会根据需要调用相应的子系统方法，并将结果返回给客户端。

外观模式适用于以下场景：

1. 当存在一个复杂的子系统，其内部有多个组件之间存在复杂的依赖关系时，可以使用外观模式将这些依赖关系隐藏起来，提供一个简化的接口给客户端使用。
2. 当客户端需要与多个子系统进行交互，并且每个子系统的接口都不一样时，可以使用外观模式将这些子系统的接口统一起来，为客户端提供一个统一的接口。
3. 当需要解耦客户端与子系统之间的依赖关系时，可以使用外观模式将它们解耦，使得客户端不需要直接与子系统交互，而只需要通过外观类进行交互。
4. 当希望对子系统进行封装和隔离，以提高系统的安全性和稳定性时，可以使用外观模式将子系统封装起来，减少对外部的暴露。

需要注意的是，外观模式并不会限制客户端直接访问子系统，如果有需要，客户端仍然可以直接使用子系统的接口。外观模式主要是为了提供一种简化和统一的访问方式，方便客户端使用和理解复杂子系统。

总之，外观模式适用于需要简化、统一接口的情况，可以降低客户端的复杂性，提供更好的封装和隔离，以及解耦客户端与子系统之间的依赖关系。

#### 适配器模式

适配器模式（Adapter Pattern）是一种结构型设计模式，用于将一个类的接口转换成客户端所期望的另一个接口。适配器模式允许原本接口不兼容的类能够一起工作。

适配器模式主要涉及三个角色：

- 目标接口（Target Interface）：客户端所期望的接口，也是客户端代码所依赖的接口。

- 适配器（Adapter）：适配器类实现了目标接口，并且持有对不兼容类的引用。它将客户端的请求转发给适配者，通过适配者来完成实际的工作。

- 适配者（Adaptee）：适配者是一个已经存在的类，它的接口与目标接口不兼容。适配器通过持有适配者的引用，将适配者的接口转换为目标接口。

适配器模式的核心思想是通过适配器类来进行接口转换，使得原本不兼容的类能够协同工作。适配器可以根据需要对适配者的接口进行包装和转换，以满足客户端的需求。

下面是一个简单的 PHP 代码示例，演示了适配器模式的使用：
```
// 目标接口
interface MediaPlayer {
    public function play($audioType, $fileName);
}

// 适配者类
class Mp3Player {
    public function playMp3($fileName) {
        echo "Playing MP3 file: $fileName\n";
    }
}

// 适配器类
class MediaAdapter implements MediaPlayer {
    private $mp3Player;

    public function __construct(Mp3Player $mp3Player) {
        $this->mp3Player = $mp3Player;
    }

    public function play($audioType, $fileName) {
        if ($audioType === 'MP3') {
            $this->mp3Player->playMp3($fileName);
        }
    }
}

// 客户端代码
$mediaPlayer = new MediaAdapter(new Mp3Player());
$mediaPlayer->play('MP3', 'song.mp3');
```
在上述示例中，我们有一个目标接口 MediaPlayer，定义了播放音频文件的方法。然后我们有一个适配者类 Mp3Player，它已经存在并有自己的方法来播放 MP3 文件。为了让 Mp3Player 能够符合目标接口，我们创建了一个适配器类 MediaAdapter，它实现了目标接口，并将实际的播放操作委托给适配者类 Mp3Player。

适配器模式的使用场景包括：

- 将旧的接口适配为新的接口：当需要使用已经存在的类，但其接口与现有的代码不兼容时，可以使用适配器模式将其适配为现有接口。

- 封装第三方库：当我们需要使用某个第三方库或服务，但其提供的接口与我们的代码不一致时，可以使用适配器模式来封装第三方库的接口，以便与我们的代码进行集成。

- 系统的扩展和升级：当系统需要扩展功能或升级时，可以使用适配器模式来兼容旧的组件或接口。这样可以避免修改已有的代码，减少对系统的影响。

总之，适配器模式可以帮助我们解决接口不兼容的问题，使不兼容的类能够一起工作。它提供了一种灵活的方式来集成和使用已有的代码和第三方库，同时保持代码的可维护性和可扩展性。在选择使用适配器模式时，需要考虑接口不兼容的程度以及对现有代码的影响，以确保适配器模式是合适的解决方案。

### 行为型模式
#### 策略模式
策略模式（Strategy Pattern）是一种行为型设计模式，它允许在运行时根据不同的策略或算法选择不同的行为。策略模式将每个行为封装成独立的类，并使它们可互相替换，从而使得算法的选择和使用与具体对象的解耦。

策略模式的核心思想是将行为抽象成独立的策略类，并通过上下文类来使用这些策略类。上下文类持有一个策略对象的引用，根据需要选择合适的策略进行执行。

策略模式涉及以下几个角色：

1. 策略接口（Strategy）：定义了策略类的公共接口，通常包含一个执行策略的方法。
2. 具体策略类（Concrete Strategy）：实现了策略接口，具体定义了不同的算法或行为。
3. 上下文类（Context）：持有一个策略对象的引用，并在需要时调用策略对象的方法来执行特定的行为。

以下是一个策略模式的示例代码：
```
// 策略接口
interface Strategy {
    public function execute();
}

// 具体策略类A
class ConcreteStrategyA implements Strategy {
    public function execute() {
        echo "ConcreteStrategyA: Executing strategy A.\n";
    }
}

// 具体策略类B
class ConcreteStrategyB implements Strategy {
    public function execute() {
        echo "ConcreteStrategyB: Executing strategy B.\n";
    }
}

// 上下文类
class Context {
    private $strategy;

    public function __construct(Strategy $strategy) {
        $this->strategy = $strategy;
    }

    public function executeStrategy() {
        $this->strategy->execute();
    }
}

// 客户端代码
$strategyA = new ConcreteStrategyA();
$strategyB = new ConcreteStrategyB();

$context = new Context($strategyA);
$context->executeStrategy();

$context->setStrategy($strategyB);
$context->executeStrategy();
```

在上述代码中，我们有一个策略接口 `Strategy`，定义了策略类的公共方法 `execute()`。具体策略类 `ConcreteStrategyA` 和 `ConcreteStrategyB` 分别实现了策略接口，并定义了不同的执行行为。

上下文类 `Context` 持有一个策略对象的引用，并在需要时调用策略对象的方法来执行特定的行为。

在客户端代码中，我们创建了具体的策略对象 `ConcreteStrategyA` 和 `ConcreteStrategyB`，并将其传递给上下文对象 `Context`。通过调用上下文对象的 `executeStrategy()` 方法，根据不同的策略选择执行不同的行为。

策略模式的优点在于它可以提供灵活性和可扩展性。通过定义独立的策略类，可以方便地增加、替换或扩展新的策略，而无需修改上下文类的代码。它能够将算法的选择与具体对象的使用分离，使得代码更加可维护和可测试。

另外，策略模式也符合开闭原则，因为可以通过添加新的策略类来扩展系统的功能，而无需修改已有的代码。

选择使用策略模式要根据具体的需求和场景进行评估。当存在多种相似但不同的算法或行为，并且需要动态地选择和切换这些算法时，策略模式是一个很好的选择。它将算法封装成策略类，使得系统更加灵活、可扩展和易于维护。

然而，如果系统中只有少量固定的算法或行为，且它们的变动性较低，使用策略模式可能会引入额外的复杂性。在这种情况下，直接在代码中使用条件语句或简单的多态可能更加简单和直观。

综上所述，选择使用策略模式需要根据具体情况进行权衡。如果需要动态地选择和切换算法或行为，或者预计将来可能有新的算法或行为添加进来，那么策略模式是一个很好的选择。
##### 使用场景
1. 多种算法或行为：当系统中存在多种相似但不同的算法或行为时，可以将每种算法或行为封装成一个策略类，通过策略模式实现灵活的算法选择。

2. 运行时动态选择：当需要在运行时根据不同条件或用户输入选择不同的算法或行为时，策略模式可以实现动态的算法选择，并避免使用大量的条件语句。

3. 避免代码重复：当系统中有多个类似的代码块，只有部分细节不同的情况下，可以使用策略模式将这些细节封装成不同的策略类，避免代码的重复。

4. 算法的扩展和维护：当系统中的算法需要经常扩展或修改时，使用策略模式可以将不同的算法封装成独立的策略类，使得扩展和维护更加方便。

5. 可测试性：策略模式可以使单元测试更加简单，因为每个策略类都可以独立测试，而不需要关注其他策略的影响。

总之，策略模式适用于需要在运行时根据不同条件选择不同算法或行为的情况，以及需要实现算法的灵活扩展和维护的场景。它能够将算法的选择与具体对象的使用分离，提供了更好的灵活性、可扩展性和可维护性。

说人话，还是我来总结吧，就是有非常多场景让用户选，但是起始条件一样，例如购买东西时使用的优惠方案，出行时选择的出行方式，同出行方式选择不同路线。

#### 观察者模式

观察者模式（Observer Pattern）是一种行为型设计模式，它定义了一种一对多的依赖关系，使得多个观察者对象可以同时监听并被通知主题对象的状态变化。

在观察者模式中，主题对象（也称为被观察者或可观察对象）维护了一个观察者列表，并提供了用于注册、删除和通知观察者的方法。观察者对象通过注册到主题对象上，以接收主题对象的状态变化通知。

当主题对象的状态发生变化时，它会依次通知所有注册的观察者对象，使得观察者对象可以根据主题对象的状态变化做出相应的更新或响应。

观察者模式涉及以下几个角色：

1. 主题接口（Subject）：定义了主题对象的接口，包括注册、删除和通知观察者的方法。
2. 具体主题类（Concrete Subject）：实现了主题接口，维护观察者列表，并在状态变化时通知观察者。
3. 观察者接口（Observer）：定义了观察者对象的接口，包括接收主题通知的方法。
4. 具体观察者类（Concrete Observer）：实现了观察者接口，实现接收主题通知的方法，并定义了具体的更新或响应逻辑。

以下是一个简单的观察者模式的示例代码：
```
// 主题接口
interface Subject {
    public function registerObserver(Observer $observer);
    public function removeObserver(Observer $observer);
    public function notifyObservers();
}

// 具体主题类
class ConcreteSubject implements Subject {
    private $observers = [];

    public function registerObserver(Observer $observer) {
        $this->observers[] = $observer;
    }

    public function removeObserver(Observer $observer) {
        $index = array_search($observer, $this->observers);
        if ($index !== false) {
            unset($this->observers[$index]);
        }
    }

    public function notifyObservers() {
        foreach ($this->observers as $observer) {
            $observer->update();
        }
    }
}

// 观察者接口
interface Observer {
    public function update();
}

// 具体观察者类
class ConcreteObserverA implements Observer {
    public function update() {
        echo "ConcreteObserverA: Received notification.\n";
    }
}

class ConcreteObserverB implements Observer {
    public function update() {
        echo "ConcreteObserverB: Received notification.\n";
    }
}

// 客户端代码
$subject = new ConcreteSubject();

$observerA = new ConcreteObserverA();
$subject->registerObserver($observerA);

$observerB = new ConcreteObserverB();
$subject->registerObserver($observerB);

$subject->notifyObservers();
```

在上述代码中，我们定义了主题接口 `Subject` 和观察者接口 `Observer`，分别定义了主题对象和观察者
对象的接口。具体主题类 `ConcreteSubject` 实现了主题接口，维护了一个观察者列表，并在状态变化时通知观察者。具体观察者类 `ConcreteObserverA` 和 `ConcreteObserverB` 实现了观察者接口，定义了接收主题通知的方法。

在客户端代码中，我们创建了具体的主题对象 `ConcreteSubject` 和具体的观察者对象 `ConcreteObserverA` 和 `ConcreteObserverB`。通过调用主题对象的 `registerObserver()` 方法注册观察者，然后调用 `notifyObservers()` 方法通知所有观察者。

当主题对象调用 `notifyObservers()` 方法时，每个观察者对象都会收到通知，并执行自己的更新逻辑。

观察者模式的优点在于它能够实现对象间的松耦合，主题对象和观察者对象之间互不依赖，可以独立地进行修改和扩展。它也提供了一种简单的触发机制，使得主题对象状态变化时能够通知到相关的观察者。

观察者模式适用于以下场景：

当一个对象的状态变化需要通知其他对象，并且不确定通知的对象有多少时，可以使用观察者模式。

当一个对象的变化需要触发一系列其他对象的更新操作时，可以使用观察者模式。

当对象之间的依赖关系需要解耦，使得对象之间的交互更加灵活和可扩展时，可以使用观察者模式。

总之，观察者模式适用于需要实现对象间的一对多依赖关系，并且希望能够实现松耦合、可扩展和可维护的场景。它能够提供一种简单而灵活的机制，使得对象之间能够以一种自动化的方式进行通信和相互作用。

### 软件设计模式

#### 依赖注入模式

依赖注入模式（Dependency Injection，简称 DI）是一种软件设计模式，旨在降低组件之间的耦合度，并促进代码的可测试性、可维护性和可扩展性。

在依赖注入模式中，一个组件的依赖关系不是在组件内部创建或解析，而是通过外部提供的方式注入到组件中。这种外部提供依赖的方式可以通过构造函数、方法参数、或者属性注入来实现。

依赖注入模式的主要目的是解耦组件之间的依赖关系，使得组件更加灵活、可重用和可测试。它可以提供以下优点：

- 降低耦合度：组件不再负责创建或解析它所依赖的对象，而是通过外部注入的方式获取依赖对象。这样，组件与具体实现类解耦，可以方便地替换依赖对象，以及支持灵活的组件组合和配置。

- 提高可测试性：依赖注入使得组件的依赖对象可以通过接口或抽象类来注入，可以轻松地创建模拟对象或假对象来进行单元测试。通过注入不同的依赖对象，可以针对不同的测试场景进行测试。

- 促进代码的可维护性和可扩展性：依赖注入将组件的依赖关系外部化，使得代码更加清晰、可读性更高，便于理解和维护。在需求变化时，可以通过注入不同的依赖对象来扩展功能，而不需要修改组件的内部实现。

下面是一个简单的 PHP 代码示例，演示了依赖注入模式的使用：
```
class Logger {
    public function log($message) {
        echo "Logging: $message\n";
    }
}

class UserService {
    private $logger;

    public function __construct(Logger $logger) {
        $this->logger = $logger;
    }

    public function registerUser($username, $password) {
        // 注册用户的逻辑...
        $this->logger->log("User registered: $username");
    }
}

// 创建依赖对象
$logger = new Logger();

// 创建服务对象，并注入依赖对象
$userService = new UserService($logger);

// 使用服务对象
$userService->registerUser("john.doe", "password");
```

在上述示例中，Logger 类是一个用于记录日志的依赖对象，UserService 类是一个用户服务类，它依赖于 Logger 类来记录日志。通过构造函数注入的方式，将 Logger 对象传递给 UserService 对象，使得 UserService 对象可以使用 Logger 对象来记录日志。

使用依赖注入模式可以有效地解耦 UserService 和 Logger 之间的依赖关系。这样做有以下好处：

- 可替换依赖对象：如果将来需要更换日志记录的方式或实现，只需创建一个新的日志记录类并实现相同的接口，然后将其注入到 UserService 中即可，而不需要修改 UserService 的代码。这样可以轻松地切换不同的日志记录方式，如将日志记录到文件、数据库或发送到远程服务器等。

- 提高可测试性：在单元测试中，我们可以使用模拟的 Logger 对象来注入到 UserService 中，以验证 UserService 的行为是否正确，而不需要实际记录日志。这样可以更方便地编写单元测试，并使测试结果更加可控。

- 易于维护和扩展：通过依赖注入，每个组件的职责清晰明确，代码更易读、理解和维护。当需要添加新的功能或修改现有功能时，我们可以通过注入不同的依赖对象来扩展或修改组件的行为，而不需要修改原有代码。这样可以保持代码的稳定性和可维护性。

总之，依赖注入模式是一种实现松耦合、提高可测试性和可维护性的重要设计模式。它通过将依赖关系从组件内部解耦，使得组件更加灵活、可扩展和易于维护。在选择使用依赖注入模式时，需要考虑组件之间的依赖关系以及代码的可测试性和可扩展性需求。