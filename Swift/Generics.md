# 何时使用

代码有多处重复实现类似的功能


# 多态

抽象代码能够面向不同的具体类型，拥有不同的行为

## 特定多态（函数超载）

* Overloads achieve `ad-hoc polymorphism`
* 函数超载，相同函数调用根据参数类型，能代表不同的事物。称之为“特定多态”（ad-hoc polymorphism）

缺点
* 生成不同的Class/Struct，实现名称相似、逻辑相似的函数，这可能会导致代码重复。

## 子类多态

* Subtypes achieve `subtype polymorphism`
* 首先有一个超类（Supper Class）运行了一段代码，其子类根据特定的类型，有不同的行为

缺点
* 运用继承让我们不得不使用引用语义，及时子类之间不需要共享任何状态，它们之间也不得不产生了一些联系
* 子类需要重载基类中的方法，如果没重载，可能要到运行的时候才会发现
```Swift
class Animal {
  func eat(_ food: ???) { fatalError("Subclass must implement 'eat'") }
}

class Cow: Animal {
  override func eat(_ food: Hay) {
    // Eat the hay
  }
}

class Chicken: Animal {
  override func eat(_ food: Grain) {
    // Peck at the grain
  }
}
```
* 最大的问题是，子类中的 `food` 的类型是不一样的，我们也可以使用 `Any` 来代替，这样我们就不得不在运行时去判断类型是否正确，会多出很多额外的判断逻辑

## 参数多态

* Generics achieve `parametic polymorphism`
* 运用泛型实现，类型本身也作为参数

# 协议

使用协议将有相似功能的对象抽象表现出来

```Swift
protocol Animal {
  associatedtype Feed: AnimalFeed
  func eat(_ food: Feed)
}

struct Cow: Animal {
  func eat(_ food: Hay) { ... }
}

struct Chicken: Animal {
  func eat(_ food: Grain) { ... }
}
```

# 泛型

```Swift
protocol Animal {
  associatedtype Feed: AnimalFeed
  func eat(_ food: Feed)
}

struct Farm {
  // 几种写法
  func feed<A: Animal>(_ animal: A) { ... }
  func feed<A>(_ animal: A) where A: Animal { ... }
  func feed(_ animal: some Animal) { ... } // This is more recommended

  // 实现
  func feed(_ animal: some Animal) {
    let crop = type(of: animal).Feed.grow()
    let produce = crop.harvest()
    animal.eat(produce)
  }
}
```
## some Animal
[some Animal]，如果第一个元素是 `Cow`，这个数组表示其他所有元素都是 `Cow`，不能是其他类型

## any Animal
* [any Animal]，表示可以是任意 `Animal` 类型，可以不同
* 静态类型：Animal
* 动态类型：具体的类型，例如前面的 `Cow`、`Chicken` 等
* 编译时，编译器会将其类型“擦除”，在运行时才会知道它的具体类型

对于 Farm，我们可以为其新增这样的函数：
```Swift
struct Farm {
  // 可以传递不同类型的 Animal 到数组中
  func feedAll(_ animals: [any Animal]) {
    for animal in animals {
      // 直接调用 `feed` 函数，将 any Animal 作为参数传递给 some Animal
      feed(animal)
    }
  }
}

```

> `some` 和 `any` 关键字都是在 `Swift5.7` 中新增的

# 参考

[Embrace Swift generics](https://developer.apple.com/wwdc22/110352)

```Swift
protocol AnimalFeed {
  associatedtype CropType: Crop where CropType.Feed == Self
  static func grow() -> CropType
}

protocol Crop {
  associatedtype Feed: AnimalFeed where Feed.CropType == Self
  func harvest() -> Feed
}

protocol Animal {
  associatedtype Feed: AnimalFeed
  func eat(_ food: Feed)
}

struct Farm {
  func feed(_ animal: some Animal) {
    let crop = type(of: animal).Feed.grow()
    let produce = crop.harvest()
    animal.eat(produce)
  }

  func feedAll(_ animals: [any Animal]) {
    for animal in animals {
      feed(animal)
    }
  }
}

struct Cow: Animal {
  func eat(_ food: Hay) {}
}

struct Hay: AnimalFeed {
  static func grow() -> Alfalfa {
    Alfalfa()
  }
}

struct Alfalfa: Crop {
  func harvest() -> Hay {
    Hay()
  }
}
```
