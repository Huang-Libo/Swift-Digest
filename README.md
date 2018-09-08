

# 简介

**版本**: `Swift 4.2` (`Xcode 10`)  
**简介**: 本文档是 Swift 官方文档的浓缩, 包含内容要点, 关键示例代码, 重要图表. 适合有一定基础的同学阅读.  

<details>
<summary>更多介绍</summary>

**创建仓库的起因**: 我去年把 `Swift` 官方文档看了一遍, 今年再回想, 很多细节都不记得了(公司项目用的是 `Objective-C`, 所以用 `Swift` 的机会也不多), 如果忘了就再读一遍, 或者需要时再去茫茫文档中查询, 感觉是在消耗时间做重复的事, 倒不如写摘要以记录官方文档的要点和关键的示例代码, 日后的查询起来效率就高多了(也有一种把书读薄的功效).
 
**阅读提示**: 由于摘要比较简洁, 所以需要读者对 `Swift` 已有一定的了解, 或者大致看过官方文档([英文原版](https://docs.swift.org/swift-book)|[中文翻译版(非官方)](https://www.cnswift.org/)). 个人推荐阅读英文版文档, 因为中文版的翻译会滞后一段时间, 并且翻译总有出错或失真部分. 可以在遇到看不懂的内容时再看中文翻译, 这样可以避免很多因翻译不准确而导致的误解.

</details>

# 目录

- [属性](https://github.com/Huang-Libo/Swift-Document-Digest#属性-properties)
- [初始化](https://github.com/Huang-Libo/Swift-Document-Digest#初始化-initialization)
    - [为存储属性设置初始化值](https://github.com/Huang-Libo/Swift-Document-Digest#为存储属性设置初始化值)
    - [自定义初始化](https://github.com/Huang-Libo/Swift-Document-Digest#自定义初始化)
    - [默认初始化器](https://github.com/Huang-Libo/Swift-Document-Digest#默认初始化器)
    - [值类型的初始化器委托](https://github.com/Huang-Libo/Swift-Document-Digest#值类型的初始化器委托)
    - [类的继承和初始化](https://github.com/Huang-Libo/Swift-Document-Digest#类的继承和初始化)
    - [可失败的初始化器](https://github.com/Huang-Libo/Swift-Document-Digest#可失败的初始化器)
    - [必要初始化器](https://github.com/Huang-Libo/Swift-Document-Digest#必要初始化器)
    - [通过闭包或函数来设置属性的默认值](https://github.com/Huang-Libo/Swift-Document-Digest#通过闭包或函数来设置属性的默认值)
- [反初始化](https://github.com/Huang-Libo/Swift-Document-Digest#反初始化-deinitialization)
- [参考资料](https://github.com/Huang-Libo/Swift-Document-Digest#参考资料)

# 属性 ([properties](https://docs.swift.org/swift-book/LanguageGuide/Properties.html))

属性把值与特定的`类`, `结构体`, 或`枚举`关联起来. 

存储属性存储常量和变量以作为实例的一部分, 反之计算属性是计算(而不是存储)值. 计算属性可由`类`, `结构体`和`枚举`定义. 存储只能被`类`和`结构体`定义.  

## 存储属性

```swift
struct FixedLengthRange {
    var firstValue: Int
    let length: Int
}
var rangeOfThreeItems = FixedLengthRange(firstValue: 0, length: 3)
// the range represents integer values 0, 1, and 2
rangeOfThreeItems.firstValue = 6
// the range now represents integer values 6, 7, and 8
```

#### 常量结构体实例的存储属性

如果你创建了一个结构体的实例并且将这个实例赋值给一个常量, 那么你不能修改这个实例的属性, 即使它们被声明为可变属性.  

```swift
let rangeOfFourItems = FixedLengthRange(firstValue: 0, length: 4)
// this range represents integer values 0, 1, 2, and 3
rangeOfFourItems.firstValue = 6
// this will report an error, even though firstValue is a variable property
```

这是因为结构体是`值类型`. 当一个`值类型`的实例被标记为常量时, 那么它的所有属性也都是常量.  

这个规则对`类`不适用, 因为`类`是`引用类型`. 如果你把引用类型的实例赋值给一个常量, 你仍然能改变那个实例的可变属性.  

#### 延迟存储属性

`延迟存储属性`是初始值直到第一次使用时才被计算的属性. 在属性的声明前添加 `lazy` 关键字, 以表明它是延迟属性.   

注意: 延迟存储属性必须总是被声明为变量(使用`var`关键字), 因为它的初始值可能在实例初始化完成后才被获取. 但是常量属性必须总是要在初始化完成前获取一个值, 因此常量属性不能被声明为 `lazy` 属性.  

注意: 如果一个被 `lazy` 修饰符标识的属性被多个线程同时访问, 并且这个属性还没有被初始化, 那么无法保证这个属性只会被初始化一次.   

#### 存储属性和实例变量

如果你有 `Objective-C` 的经验, 你可能知道它的类的实例有两种方式来存储值和引用. 除了*属性*, 你可以使用*实例变量*作为存储在属性中的值的后备存储.  

Swift 将这两种概念统一到单一的属性声明中. Swift 属性没有对应的*实例变量*, 并且属性的后备存储不能被直接访问. 这种机制避免了值在不同的上下文中是如何访问的困惑, 并且将属性的声明简化为一个单一的, 限定的语句. 属性的所有信息---包括它的名字, 类型, 和内存管理特性, 都作为这个类型的定义放在同一个地方.  

# 初始化 ([initialization](https://docs.swift.org/swift-book/LanguageGuide/Initialization.html))

指初始化`类`, `结构体`, 或`枚举`的实例, 并给`存储属性`赋值等操作.

和 `Objective-C` 的初始化器不同的是, `Swift` 的初始化器不返回值.

类的实例还可以实现一个`反初始化器`.

## 为存储属性设置初始化值

> `类`和`结构体`在创建实例时, 必须给他们的`存储属性`设置一个合适的初始值. (通过初始化器设置, 或者在定义时给一个默认值, 这两种途径都不会触发`属性观察`)  

如果一个存储属性**总是使用固定值**, 最好是在定义时就给定初始化值, 而不是在初始化器中设置. 这样更简洁易读, 并且有利于 Swift 根据默认值去推断属性的类型, 还可以使我们更容易地利用[默认初始化器](https://github.com/Huang-Libo/Swift-Document-Digest#默认初始化器)和[初始化器继承](https://github.com/Huang-Libo/Swift-Document-Digest#类的继承和初始化). 例如, 如果 temperature 属性是一个固定值:


推荐:

```swift
struct Fahrenheit {
    var temperature = 32.0
}
```

不推荐:

```swift
struct Fahrenheit {
    var temperature: Double
    init() {
        temperature = 32.0
    }
}
```

## 自定义初始化

#### 参数名和内容提要标签

`参数名(parameter name)`: 在内部使用.  
`内容提要标签(argument label)`: 在调用时使用.  

这和`函数`和`方法`是一样的.

#### 可选属性类型

可选属性会被自动初始化为 nil.  

#### 初始化时给常量属性赋值

常量被赋值后, 就不能再更改了.   
注意: 对`类`而言, 常量属性只能在本类初始化时被修改, 而不能被其子类修改.  

## 默认初始化器

如果一个`结构体`或`类`给它所有的属性都设置了默认值, 并且自身一个初始化器也没有提供(比如: 没有父类, 自身也没实现初始化器), 那么 Swift 就会为它提供一个`默认初始化器`. 例如:

```swift
// 属性都有默认值, 类没有实现初始化器(也没有父类), 
// 所以 Swift 会提供一个默认初始化器.
class ShoppingListItem {
    var name: String?
    var quantity = 1
    var purchased = false
}
var item = ShoppingListItem()
```

#### 结构体类型的成员初始化器

和`默认初始化器`不同的是, 结构体的`成员初始化器(memberwise initializer)`不需要其存储属性有默认值. 

```swift
struct Size {
    var width, height: Double
}
let twoByTwo = Size(width: 2.0, height: 2.0)
```

## 值类型的初始化器委托

`初始化器委托(Initializer Delegation)`: 初始化器可以调用其他初始化器来完成实例的部分初始化, 这样可以减少冗余代码.  

注意: 
1. `值类型`和`类类型`的初始化器规则不同, 值类型(结构体和枚举)不支持继承, 所以他们的初始化器委托过程相对简单. 类类型支持继承, 所以它要保证其所继承的所有存储属性都要在初始化时赋一个合适的值.
2. 如果你给值类型定义了一个自定义的初始化器, 那么你将无法访问`默认初始化器`(或者`成员初始化器`, 对结构体而言). 这个限制防止别人意外地使用自动初始化器而把复杂初始化器里提供的额外必要配置给绕开的情况发生.  
3. 如果你想让值类型使用默认初始化器或成员初始化器, 同时也使用自定义初始化器, 那么请在`拓展(Extension)`中实现自定义初始化器.

对值类型而言, 可以通过 `self.init`来调用其他初始化器, `self.init`只能在初始化器中调用.

实例: 

```swift
struct Size {
    var width = 0.0, height = 0.0
}
struct Point {
    var x = 0.0, y = 0.0
}
```

```swift
struct Rect {
    var origin = Point()
    var size = Size()
    // 初始化器一
    init() {}
    // 初始化器二
    init(origin: Point, size: Size) {
        self.origin = origin
        self.size = size
    }
    // 初始化器三
    init(center: Point, size: Size) {
        let originX = center.x - (size.width / 2)
        let originY = center.y - (size.height / 2)
        self.init(origin: Point(x: originX, y: originY), size: size)
    }
}
```

解读:  

第一个初始化器, 和`默认初始化器`功能相同.
```swift
let basicRect = Rect()
// basicRect's origin is (0.0, 0.0) and its size is (0.0, 0.0)
```

第二个初始化器, 和`成员初始化器`功能相同.
```swift
let originRect = Rect(origin: Point(x: 2.0, y: 2.0),
                      size: Size(width: 5.0, height: 5.0))
// originRect's origin is (2.0, 2.0) and its size is (5.0, 5.0)
```

第三个初始化器中调用了第二个初始化器(初始化器委托).
```swift
let centerRect = Rect(center: Point(x: 4.0, y: 4.0),
                      size: Size(width: 3.0, height: 3.0))
// centerRect's origin is (2.5, 2.5) and its size is (3.0, 3.0)
```

如果不想自己实现 `init()` and `init(origin:size:)`, 则需要在`扩展(Extension)`中实现 `init(center:size:)` 初始化器.  

## 类的继承和初始化

Swift 为`类类型`定义了两种初始化器来确保所有的存储属性(包括自身的和继承的)都接收到一个初始值: `指定初始化器(designated initializer)`和`便捷初始化器(convenience initializer)`.

#### 指定初始化器和便捷初始化器

类的指定初始化器的语法(和值类型的语法相同):
```swift
init(parameters) {
    statements
}
```

类的便捷初始化器的语法:
```swift
convenience init(parameters) {
    statements
}
```

#### 类类型的初始化器委托

类类型的初始化器委托遵循三个规则:
1. 指定初始化器必须调用其直接父类的指定初始化器.
2. 便捷初始化器必须调用*同一个类*中的初始化器.
3. 便捷初始化器必须最终调用到指定初始化器.

示例1:  
<img src="./media/initializerDelegation01_2x.png" width="70%" height="70%">

示例2:  
<img src="./media/initializerDelegation02_2x.png" width="70%" height="70%">

#### 两段式初始化

Swift 中的类初始化是一个两段式过程:  
*第一阶段*: 每一个存储属性都被引入它的类赋一个初始值.(方向: 从子类到父类)  
*第二阶段*: 每个类定制其存储属性(包括自身引入的和继承的存储属性).(方向: 从父类到子类)  

两段式初始化的好处:   

1. 防止属性值在初始化之前被访问;  
2. 防止属性值被另一个初始化器意外地赋予不同的值.  

注意: Swift 的两段式初始化过程和 Objective-C 类似. 他们的主要区别体现在第一阶段, Objective-C 给每个属性分配零或者空值(比如 0 或 nil ). Swift 的初始化流程更加灵活, 它允许你设置自定义的初始值, 并可以应对 0 或 nil 不为某些类型的合法初始值的情况.     

Swift 的编译器执行4个有效的安全性检查来确保两段式初始化顺利完成.  

*安全检查 1*  

一个指定初始化器必须先完成对该类引入的存储属性的初始化, 然后再向上委托给父类的初始化器.  

*安全检查 2*  

指定初始化器必须先向上委托给父类, 然后再给继承来的属性赋值. 否则这个初始化器对继承属性的赋值会被父类的初始化器覆盖.  

*安全检查 3*  

便捷初始化器必须先委托给另一个初始化器, 然后再给属性赋值(包括本类引入的属性). 否则便捷初始化器设置的新值会被本类的指定初始化器覆盖.  

*安全检查 4*  

在第一阶段完成之前, 初始化器不能调用实例方法, 读取实例属性的值, 或者引用 self 作为值.  

基于以上的四个安全检查, 两段式初始化的流程是这样的:  

*第一阶段*  

- 指定初始化器或便捷初始化器在类中被调用.
- 为这个类的新实例分配内存, 内存还没有初始化.
- 这个类的指定初始化器确保它自身引入的所有存储属性都有一个值. 这些存储属性的内存现在就初始化好了.
- 这个指定初始化器 hands off to (翻译成:"上交给"吗?)父类的初始化器, 为其存储属性执行相同的任务.
- 这个过程沿着继承链向上执行, 直到到达继承链的顶端.
- 到达继承链顶端后, 顶端的这个类确保了其所有的存储属性都有一个值, 这个实例的内存就被认为完成了初始化, 第一阶段完成.

<img src="./media/twoPhaseInitialization01_2x.png" width="70%" height="70%">  

*第二阶段*  

- 从继承链顶部往下执行, 每一个指定初始化器都有机会来进一步定制实例, 初始化器现在可以访问 self 和修改他的属性, 调用实例方法, 等等.
- 最后, 继承链上的任何便捷初始化器都有机会来定制这个实例和使用 self.

<img src="./media/twoPhaseInitialization02_2x.png" width="70%" height="70%">  


#### 初始化器的继承和重写

和 `Objective-C` 的子类不一样, Swift 的子类*默认*不会继承他们父类的初始化器. Swift 的这种机制可防止父类的简单初始化器被更定制化的子类继承, 并且用来创建了一个未被完全或正确初始化的子类实例.

当你写的一个子类初始化器和父类的指定初始化器相匹配时, 你实际上是在重写父类的指定初始化器. 因此, 你必须在子类的初始化器定义前写上 `override` 修饰符. 即使被重写的父类初始化器是一个自动提供的`默认初始化器`, 规则也是如此.

注意: 当你重写父类的指定初始化器时, 你总是得写 `override` 修饰符, 即使你的子类初始化器的实现是一个便捷初始化器.

相反地, 如果你如果你写了一个子类初始化器匹配父类的*便捷初始化器*, 那么父类的便捷初始化器永远无法被这个子类直接调用(因为便捷初始化器必须调用同类的初始化器). 所以, 严格来讲, 这个子类不是在重写父类的初始化器. 因此, 不要在初始化器的定义前添加 `override` 修饰符.



#### 初始化器的自动继承

如前所述, 默认情况下子类不会继承父类的初始化器. 然而, 当满足特定条件时, 父类的初始化器会被自动继承.

假设你为子类引入的任何新属性都提供了默认值, 以下的两条规则将适用:

*规则一*

如果你的子类没有定义任何指定初始化器, 它会自动继承父类所有的指定初始化器.

*规则二*

如果你的子类为父类的所有指定初始化器提供了实现---无论是按照`规则一`继承而来的, 还是作为子类定义的一部分自己实现的, 它都会自动继承父类的所有便捷初始化器.

#### 指定初始化器和便捷初始化器实战

以下这个例子三个层级的类: Food, RecipeIngredient, ShoppingListItem, 并且展示了它们的初始化器是如何交互的.  

这个层级中的基类是 `Food`:   

```swift
class Food {
    var name: String
    init(name: String) {
        self.name = name
    }
    convenience init() {
        self.init(name: "[Unnamed]")
    }
}
```

<img src="./media/initializersExample01_2x.png" width="70%" height="70%">  

```swift
let namedMeat = Food(name: "Bacon")
// namedMeat's name is "Bacon"

let mysteryMeat = Food()
// mysteryMeat's name is "[Unnamed]"
```

这个层级中的第二个类  `RecipeIngredient` 是 `Food` 的子类:  

```swift
class RecipeIngredient: Food {
    var quantity: Int
    init(name: String, quantity: Int) {
        self.quantity = quantity
        super.init(name: name)
    }
    override convenience init(name: String) {
        self.init(name: name, quantity: 1)
    }
}
```

<img src="./media/initializersExample02_2x.png" width="70%" height="70%">  

`RecipeIngredient` 的 `init(name: String)` 便捷初始化器和 `Food` 中的 `init(name: String)` 指定初始化器带有相同的参数. 由于这个便捷初始化器重写了父类的指定初始化器, 所以它必须使用 `override` 修饰.  

由于 `RecipeIngredient` 为父类的所有指定初始化器提供了实现, 所以 `RecipeIngredient` 自动地继承了父类的所有便捷初始化器. 在这个例子中, 父类 `Food` 只有一个叫 `init()` 的便捷初始化器, 这个初始化器就被 `RecipeIngredient` 继承了. 继承而来的 `init()` 和 `Food` 类中的行为是一样的, 除了它将 `init(name: String)` 委托给了 `RecipeIngredient` 中的那个版本, 而不是 `Food` 中的那个版本.  

```swift
let oneMysteryItem = RecipeIngredient()
let oneBacon = RecipeIngredient(name: "Bacon")
let sixEggs = RecipeIngredient(name: "Eggs", quantity: 6)
```

层级中的最后一个类是 `RecipeIngredient` 的子类, 叫做 `ShoppingListItem`:  

```swift
class ShoppingListItem: RecipeIngredient {
    var purchased = false
    var description: String {
        var output = "\(quantity) x \(name)"
        output += purchased ? " ✔" : " ✘"
        return output
    }
}
```

因为这个类为自身所引入的所有属性都提供了一个默认值, 并且自身没有定义任何初始化器, 所以 `ShoppingListItem` 自动地继承了父类的所有指定初始化器和便捷初始化器.       

<img src="./media/initializersExample03_2x.png" width="70%" height="70%">  

```swift
var breakfastList = [
    ShoppingListItem(),
    ShoppingListItem(name: "Bacon"),
    ShoppingListItem(name: "Eggs", quantity: 6),
]
breakfastList[0].name = "Orange juice"
breakfastList[0].purchased = true
for item in breakfastList {
    print(item.description)
}
// 1 x Orange juice ✔
// 1 x Bacon ✘
// 6 x Eggs ✘
```

## 可失败的初始化器

有的时候为`类`, `结构体`, 或`枚举`定义可失败的初始化器是有用的.  

在 `init` 关键字后面添加一个问号就是可失败的初始化器(`init?`).  

注意: 不能定义*含有相同参数类型和名称*的可失败和非可失败初始化器.  

可失败初始化器会创建一个该类的可选值. 你在可失败初始化器中写 `return nil` 来触发初始化失败.  

注意: 严格来讲, 初始化器没有返回值. 它们的角色是确保初始化结束时 `self` 被完全和正确地初始化了, 尽管你写 `return nil` 来触发初始化失败, 但是你不能用 `return` 关键字来表示初始化成功.  

例如, 可失败的初始化器为数值类型转换做了实现. 要确保数值类型间的转换保持值不变, 使用 `init(exactly:)` 初始化器. 如果类型转换不能保持值不变, 那么初始化失败.  

```swift
let wholeNumber: Double = 12345.0
let pi = 3.14159

if let valueMaintained = Int(exactly: wholeNumber) {
    print("\(wholeNumber) conversion to Int maintains value of \(valueMaintained)")
}
// Prints "12345.0 conversion to Int maintains value of 12345"

let valueChanged = Int(exactly: pi)
// valueChanged is of type Int?, not Int

if valueChanged == nil {
    print("\(pi) conversion to Int does not maintain value")
}
// Prints "3.14159 conversion to Int does not maintain value"
```

另一个自定义类的例子:  

```swift
struct Animal {
    let species: String
    init?(species: String) {
        if species.isEmpty { return nil }
        self.species = species
    }
}


let someCreature = Animal(species: "Giraffe")
// someCreature is of type Animal?, not Animal

if let giraffe = someCreature {
    print("An animal was initialized with a species of \(giraffe.species)")
}
// Prints "An animal was initialized with a species of Giraffe"


let anonymousCreature = Animal(species: "")
// anonymousCreature is of type Animal?, not Animal

if anonymousCreature == nil {
    print("The anonymous creature could not be initialized")
}
// Prints "The anonymous creature could not be initialized"
```

#### 枚举的可失败初始化器

```swift
enum TemperatureUnit {
    case kelvin, celsius, fahrenheit
    init?(symbol: Character) {
        switch symbol {
        case "K":
            self = .kelvin
        case "C":
            self = .celsius
        case "F":
            self = .fahrenheit
        default:
            return nil
        }
    }
}

let fahrenheitUnit = TemperatureUnit(symbol: "F")
if fahrenheitUnit != nil {
    print("This is a defined temperature unit, so initialization succeeded.")
}
// Prints "This is a defined temperature unit, so initialization succeeded."

let unknownUnit = TemperatureUnit(symbol: "X")
if unknownUnit == nil {
    print("This is not a defined temperature unit, so initialization failed.")
}
// Prints "This is not a defined temperature unit, so initialization failed."
```

#### 带有原始值枚举的可失败初始化器

带有原始值的枚举会自动获得一个可失败的初始化器, `init?(rawValue:)`. `rawValue` 参数的类型由对应的 raw-value 决定.  

```swift
enum TemperatureUnit: Character {
    case kelvin = "K", celsius = "C", fahrenheit = "F"
}

let fahrenheitUnit = TemperatureUnit(rawValue: "F")
if fahrenheitUnit != nil {
    print("This is a defined temperature unit, so initialization succeeded.")
}
// Prints "This is a defined temperature unit, so initialization succeeded."

let unknownUnit = TemperatureUnit(rawValue: "X")
if unknownUnit == nil {
    print("This is not a defined temperature unit, so initialization failed.")
}
// Prints "This is not a defined temperature unit, so initialization failed."
```

#### 初始化失败的传递

`类`, `结构体`, 或`枚举`的可失败初始化器能委托给同一个`类`, `结构体`, 或`枚举`中的另一个可失败初始化器, 类似的, 子类的可失败初始化器可向上委托给父类的可失败初始化器.

注意: 可失败初始化器也可以委托给不可失败初始化器. Use this approach if you need to add a potential failure state to an existing initialization process that does not otherwise fail. (这句话怎么理解? 需要用实例来作说明)

```swift
class Product {
    let name: String
    init?(name: String) {
        if name.isEmpty { return nil }
        self.name = name
    }
}

class CartItem: Product {
    let quantity: Int
    init?(name: String, quantity: Int) {
        if quantity < 1 { return nil }
        self.quantity = quantity
        super.init(name: name)
    }
}

if let twoSocks = CartItem(name: "sock", quantity: 2) {
    print("Item: \(twoSocks.name), quantity: \(twoSocks.quantity)")
}
// Prints "Item: sock, quantity: 2"


if let zeroShirts = CartItem(name: "shirt", quantity: 0) {
    print("Item: \(zeroShirts.name), quantity: \(zeroShirts.quantity)")
} else {
    print("Unable to initialize zero shirts")
}
// Prints "Unable to initialize zero shirts"


if let oneUnnamed = CartItem(name: "", quantity: 1) {
    print("Item: \(oneUnnamed.name), quantity: \(oneUnnamed.quantity)")
} else {
    print("Unable to initialize one unnamed product")
}
// Prints "Unable to initialize one unnamed product"
```

#### 重写一个可失败初始化器

你可以在子类中重写父类的可失败初始化器, 就和其他任何初始化器一样. 或者, 你可以在子类中使用*不可失败*初始化器来重写父类的可失败初始化器. 这使你能够在子类中定义一个不可失败的初始化器, 尽管其父类对应的初始化器允许失败.  

要注意的是如果你用不可失败的子类初始化器重写了父类的可失败初始化器, 委托给父类初始化器的唯一方式是对父类可失败初始化器的结果强制解包.  

注意: 你可以用一个不可失败初始化器来重写一个可失败初始化器, 但是反过来不行.  

在以下的例子中, `Document` 类的 `name` 属性可以是非空字符串或者 `nil`, 但是不能是一个空字符串.

```swift
class Document {
    var name: String?
    // this initializer creates a document with a nil name value
    init() {}
    // this initializer creates a document with a nonempty name value
    init?(name: String) {
        if name.isEmpty { return nil }
        self.name = name
    }
}
```

接下来定义了 `Document` 的子类, 叫做 `AutomaticallyNamedDocument`. 它重写了 `Document` 中的两个指定初始化器. 

```swift
class AutomaticallyNamedDocument: Document {
    override init() {
        super.init()
        self.name = "[Untitled]"
    }
    override init(name: String) {
        super.init()
        if name.isEmpty {
            self.name = "[Untitled]"
        } else {
            self.name = name
        }
    }
}
```

`AutomaticallyNamedDocument` 使用不可失败的 `init(name:)` 初始化器重写了父类的可失败初始化器 `init?(name:)`. 由于 `AutomaticallyNamedDocument` 处理空字符串的规则和父类的不同, 它的初始化器不需要失败, 所以它提供了一个不可失败版本的初始化器.  

你可以在一个初始化器中使用强制解包来调用父类中的可失败初始化器, 以作为子类的不可失败初始化器实现的一部分. 例如, 下面的 `UntitledDocument` 子类总是把 name 属性命名为 "[Untitled]", 并且它在初始化的时候使用了父类的可失败初始化器 `init(name:)`. 

由于 name 参数总是有值, 所以强制解包不会产生运行时错误.

```swift
class UntitledDocument: Document {
    override init() {
        super.init(name: "[Untitled]")!
    }
}
```

#### init! 可失败初始化器

通常来讲我们通过在 `init` 关键字后添加问号 ( `init?` )的方式来定义一个可失败初始化器以创建一个合适类型的可选项实例. 另外, 你也可以使用可失败初始化器创建一个**隐式展开**具有合适类型的可选项实例. 通过在 init 后面添加惊叹号( `init!` )是不是问号.  

你可以在 `init?` 初始化器中委托调用 `init!` 初始化器, 反之亦然. 你也可以用 `init!` 重写 `init?`, 反之亦然. 你还可以用 `init` 委托调用 `init!` , 尽管当 `init!` 初始化器导致初始化失败时会触发断言.  

##  必要初始化器

在`类`的初始化器前面写 `required` 修饰符来表示这个类的所有子类都必须实现这个初始化器:

```swift
class SomeClass {
    required init() {
        // initializer implementation goes here
    }
}
```

你也必须在每个子类实现的必要初始化器前面写上 `required` 修饰符, 来表示初始化器的必要性在继承链的子类上继续适用. 当你重写一个*必要*指定初始化器时, 不需要写 `override` 修饰符.  

```swift
class SomeSubclass: SomeClass {
    required init() {
        // subclass implementation of the required initializer goes here
    }
}
```

## 通过闭包或函数来设置属性的默认值

如果一个存储属性的默认值需要一些自定义或设置, 你可以使用一个`闭包`或`全局函数`来为那个属性提供自定义的默认值.  

```swift
class SomeClass {
    let someProperty: SomeType = {
        // create a default value for someProperty inside this closure
        // someValue must be of the same type as SomeType
        return someValue
    }()
}
```

注意闭包结尾处紧跟着一对圆括号, 这告诉 Swift 去立即执行这个闭包.  

注意: 如果你使用一个闭包去初始化属性, 要记住当闭包执行时, 该实例的剩余部分还没有初始化好, 这意味着在闭包内你不能访问任何属性值, 即使那些属性有默认值. 你也不能隐式使用 `self` 属性, 或者调用任何实例方法.  


# 反初始化 ([deinitialization](https://docs.swift.org/swift-book/LanguageGuide/Deinitialization.html))

反初始化器在类的实例释放之前立即调用. 只有`类`类型有反初始化器.  

Swift 也是通过 ARC(Automatic Reference Counting) 来管理内存的. 一般情况下当实例销毁时你不需要做手动清理. 但是, 如果你在使用你自己的资源 , 你就需要做一些额外的清理工作. 比如, 你创建了一个自定义的类来打开一个文件并且写入一些数据, 那么你需要在类的实例销毁前关闭这个文件.  

一个类最多只能有一个反初始化器, 反初始化器不带任何参数并且没有圆括号:  

```swift
deinit {
    // perform the deinitialization
}
```

反初始化器是在实例即将要被释放前自动调用的. 你不能自己调用反初始化器. 父类的反初始化器会被子类自动继承, 并且父类的反初始化器会在子类的反初始化器实现的最后面自动调用. 父类的反初始化器总是会被调用, 即使子类没有提供反初始化器.  

因为实例是在反初始化器调用后才被释放, 所以在反初始化器里面,    可以获取这个实例的所有属性, 并且可以基于这些属性修改实例自身的行为. (比如查找需要关闭的文件的名称)  

# 参考资料

- 官方文档(英文): https://docs.swift.org/swift-book  
- 官方文档的中文翻译(非官方翻译): https://www.cnswift.org/  
- http://swiftguide.cn/  


