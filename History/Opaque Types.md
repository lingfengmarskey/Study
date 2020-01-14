
### Opaque Types 不透明类型

一个函数或者方法,带有不透明的返回值,隐藏了它的返回值的类型信息.它通过在其所支持的协议条款中描述其返回值,来替代在函数中提供明确类型的返回值.隐藏类型信息在让模块和模块中所调用的代码边界清晰 这方面很有用,因为返回值的基本类型合一保持私有. 不同于返回值类型是协议, 不透明类型保持了类型标记---编译器可以访问类型信息, 但模块端不能访问.

#### 不透明类型解决的问题

比如,你要写一个画文字画的模块.它最主要的特点就是,通过`draw()`函数来返回一个表示形状的字符串,你可以把它作为`shape`协议的需求.

```Swift
protocol Shape {
    func draw() -> String
}
	
struct Triangle: Shape {
    var size: Int
    func draw() -> String {
        var result = [String]()
        for length in 1...size {
            result.append(String(repeating: "*", count: length))
        }
        return result.joined(separator: "\n")
    }
}
let smallTriangle = Triangle(size: 3)
print(smallTriangle.draw())
// *
// **
// ***
```

你可以使用泛型来实现一些操作, 比如下面代码写的垂直翻转形状. 但是这个方法有个非常重要的限制:翻转的结果显示了 这个确切的泛型是用来创建它的.

```Swift
struct FlippedShape<T: Shape>: Shape {
    var shape: T
    func draw() -> String {
        let lines = shape.draw().split(separator: "\n")
        return lines.reversed().joined(separator: "\n")
    }
}
let flippedTriangle = FlippedShape(shape: smallTriangle)
print(flippedTriangle.draw())
// ***
// **
// *
```

这个方法定义了一个竖直连接两个形状的结构体`JoinedShape<T: Shape, U: Shape>`,就像下面写的那样,连接一个翻转的三角形和三角形的结果导致类型是这样:`JoinedShape<FlippedShape<Triangle>, Triangle>`.

```Swift
struct JoinedShape<T: Shape, U: Shape>: Shape {
    var top: T
    var bottom: U
    func draw() -> String {
        return top.draw() + "\n" + bottom.draw()
    }
}
let joinedTriangles = JoinedShape(top: smallTriangle, bottom: flippedTriangle)
print(joinedTriangles.draw())
// *
// **
// ***
// ***
// **
// *
```

暴露创建一个形状的细节, 允许那些本并不属于字符画模块公共接口的类型泄露出去, 是因为需要声明完整的返回值类型.模块内部的代码能用各种方式构建相同的形状,而模块外部的代码,不应该对这一系列的变换细节作出任何解释.封装的类型比如`JoinedShape`和`FlippedShape`不管先模块的用户, 他们不应该可见. 模块的公共接口有一系列的操作构成,像是连接或者翻转形状,这些操作会返回另个一个`Shape`的值.

#### 返回一个不透明类型

你可以把 不透明类型看做是 泛型的逆向.泛型让调用函数的代码通过一种方式,选择函数参数和返回值的类型,这种方式是从函数的实现中抽象出来的. 比如下面这段代码, 他的返回值类型依赖于它的调用者.

 ```Swift
 func max<T>(_ x: T, _ y: T) -> T where T: Comparable { ... }
 ```
调用函数`max(_:_:)`的代码选择x和y的值,他们的类型决定了`T`的确切类型. 调用的代码可以使用任何遵循了`Comparable`协议的类型. 函数内部是以通用的方式写的,所以它能处理调用者提供的任何类型. 函数`max(_:_:)`仅能使用`Comparable`协议所共享的功能.

在使用不透明类型的返回值的函数当中,它们的角色逆转了. 不透明类型让函数的实现为了返回值而挑选类型, 通过从调用函数的代码中抽象出来的. 比如下面这段函数, 返回了一个梯形,而并没有暴露`Shape`的基本类型.

```Swift
struct Square: Shape {
    var size: Int
    func draw() -> String {
        let line = String(repeating: "*", count: size)
        let result = Array<String>(repeating: line, count: size)
        return result.joined(separator: "\n")
    }
}

func makeTrapezoid() -> some Shape {
    let top = Triangle(size: 2)
    let middle = Square(size: 2)
    let bottom = FlippedShape(shape: top)
    let trapezoid = JoinedShape(
        top: top,
        bottom: JoinedShape(top: middle, bottom: bottom)
    )
    return trapezoid
}
let trapezoid = makeTrapezoid()
print(trapezoid.draw())
// *
// **
// **
// **
// **
// *
```

例子中的函数`makeTrapesoid()`声明其返回值类型为`some Shape`,结果函数返回了一个遵循`Shape`协议,而又没有指定确切类型的值.用这种方式去写函数`makeTrapesoid()`,能表现其公共接口的基础面---返回值是一个`Shape` ---又能不指定返回值类型, 是由其公共接口产生的. 这个实现使用了两个三角形和一个矩形, 但该函数可以通过各种方式被重写来绘制梯形而并不改变他的返回值.

这个例子强调了不透明类型就像泛型的逆向. 函数`makeTrapesoid()`内部可以返回任何需要的类型,只要这个类型遵循`Shape`协议,就像调用代码为泛型函数所做的那样. 调用函数的代码需要被重写得通用一些, 就像泛型函数的实现一样, 让它可以和任何`Shape`的值一起正常运行.

也可以同时使用不透明类型和泛型. 下面的函数同时返回了遵循`Shape`协议的值.

```Swift
func flip<T: Shape>(_ shape: T) -> some Shape {
    return FlippedShape(shape: shape)
}
func join<T: Shape, U: Shape>(_ top: T, _ bottom: U) -> some Shape {
    JoinedShape(top: top, bottom: bottom)
}

let opaqueJoinedTriangles = join(smallTriangle, flip(smallTriangle))
print(opaqueJoinedTriangles.draw())
// *
// **
// ***
// ***
// **
// *
```

这个例子中的`opaqueJoinedTriangles`的值和之前在泛型的例子中的`joinedTriangles`的值是一样的. 但是, 跟那个例子不同,函数`flip(_:)`和`join(_:_:)`封装了基础类型,让通用操作返回一个不透明的类型, 保护了类型的可见性. 两个函数都是通用的,因为他们依赖的类型都是通用的, 并且函数的类型参数传递了`FlippedShape`和`JoinedShape`所需的类型信息.


如果一个返回值是不透明类型的函数,从多处返回值, 所有可能的返回值必须有相同的类型. 对于通用函数来说, 返回类型可以使用函数的泛型参数, 但是他必须是一个类型.比如,这有一个关于形状翻转函数的不合法版本,它对矩形有中特殊的场景.

```Swift
func invalidFlip<T: Shape>(_ shape: T) -> some Shape {
    if shape is Square {
        return shape // Error: return types don't match
    }
    return FlippedShape(shape: shape) // Error: return types don't match
}
```
如果你用`Square`调用这个函数, 它返回一个`Square`; 否则他返回一个`FlippedShape`. 这违反了返回值只有一种类型的需求, 同时让`invalidFlip(_:)`称为无效代码. 一种修复方式是把`squares`的特殊情的代码移动到`FlippedShape`的实现里, 让这个函数总是返回一个`FlippedShape`值.

```Swift
struct FlippedShape<T: Shape>: Shape {
    var shape: T
    func draw() -> String {
        if shape is Square {
            return shape.draw()
        }
        let lines = shape.draw().split(separator: "\n")
        return lines.reversed().joined(separator: "\n")
    }
}
```
总是返回一种类型的需求,并没有阻止你在使用不透明返回类型的时候使并使用泛型.这有一个函数的例子,它把函数的类型包含到返回值的基本类型当中.

```Swift
func `repeat`<T: Shape>(shape: T, count: Int) -> some Collection {
    return Array<T>(repeating: shape, count: count)
}
```

这种情况下, 返回值变量的基本类型依赖于`T`: 不管什么`Shape`被传递, `repeat(shape:count:)`函数都会穿件和返回`Shape`的数组. 尽管如此,返回值总是有同样的基本类型`[T]`, 所以拥有不透明类型的返回值的函数,只能返回一种类型.


#### 不透明类型和协议类型的区别

返回一个不透明类型看起来和使用协议类型作为返回值非常相似, 但这两种返回值类型在是否保持类型标识上面有区别. 一个不透明类型可以所引导一个确切的类型, 尽管调用者并不知道它是啥; 而协议类型可以指向任何遵循协议的类型. 通常来讲, 协议类型给你更多的关于存储的值的基本类型的灵活性 . 而不透明类型让你对那些基本类型有更强的保护.

比如,这一个版本的函数,它返回了一个协议类型.

```Swift
func protoFlip<T: Shape>(_ shape: T) -> Shape {
    return FlippedShape(shape: shape)
}
```
这个版本的函数`protoFlip( _:) `和`flip( _:)`的body一样, 另外它总是返回同样的类型. 不同于`flip( _:)`, 函数`protoFlip( _:)`的返回值不需要总是同一个类型 --- 它只是遵循`Shape`协议即可. 换句话来说, `protoFlip( _:)`函数对于他的调用者的接口约束比`flip( _ : )`要更松一些. 它对于多种类型的返回值保持了灵活性.

```Swift
func protoFlip<T: Shape>(_ shape: T) -> Shape {
    if shape is Square {
        return shape
    }

    return FlippedShape(shape: shape)
}
```

修改后的代码要看传入的什么类型的`Shape`来决定返回的实例是`Square`或者`FlippedShape`的实例.通过这个函数翻转的图形可以是不同的类型. 当你使用同样形状的多种实例的情况下, 这个函数的其他版本可以返回不同类型.函数`protoFlip( _ : )`的返回值类型的不太明确意味着返回值上依靠类型信息的操作都不可用. 比如说, 不能该函数的返回值不能利用 `==`操作符来比较结果.

```Swift
let protoFlippedTriangle = protoFlip(smallTriangle)
let sameThing = protoFlip(smallTriangle)
protoFlippedTriangle == sameThing  // Error
```

例子最后一行错误的发生,有几个原因. 直接的问题是`Shape`协议需求中并不包括`==`操作符. 如果你尝试添加一个, 紧接着将会遇到的问题是, 需要知道`==`操作符左边和右边的参数类型. 这种类型的操作 通常用类型`Self`获取参数, 匹配遵循协议的任何具体类型, 但是向协议中添加`Self`需求在类型擦除中的情况下(是指使用协议作为类型)是不被允许的.

使用协议类型作为返回值给你很大的灵活性,只要返回值遵循协议即可. 但是灵活性的代价是返回值是失去一些操作上的可能性.上面的例子显示了`==`操作符怎么不可用的. (它依赖于具体的类型信息,使用协议类型时并没有保存类型信息)

这个方法的另一个问题是, 形状变换不能嵌套. 翻转一个三角形的值是一个`Shape`协议类型的值, 函数`protoFlip( _ : )`需要遵循`Shape`协议的类型作为参数. 但是协议类型的值并不遵循协议本身; 函数`protoFlip( _ : )`的返回值并不遵循`Shape`协议. 这意味着向`protoFlip(protoFlip(smallTriange))`这样的应用了多次的代码是无效的,因为被折叠了的形状不是一个可用的参数,对于`protoFlip( _ : )`来说.

于此相反, 不透明类型存储了基本类型标识. Swift可以推断到关联的类型,让你在不能使用协议作为返回值的地方用不透明类型来替代.比如,这有一个泛型中`Container`协议的例子.

```Swift
protocol Container {
    associatedtype Item
    var count: Int { get }
    subscript(i: Int) -> Item { get }
}
extension Array: Container { }

```

你不能用`Container`作为函数的返回值类型,因为这个协议有关联类型.你也不能用它作为一个限制性的通用返回值类型, 因为函数体外没有足够的信息来推断泛型的类型.

```Swift
// Error: Protocol with associated types can't be used as a return type.
func makeProtocolContainer<T>(item: T) -> Container {
    return [item]
}

// Error: Not enough information to infer C.
func makeProtocolContainer<T, C: Container>(item: T) -> C {
    return [item]
}

```

使用不透明类型`some 	Container` 作为返回值表现接口描述, 函数返回一个`container` 但是不指定`container`的类型.

```Swift
func makeOpaqueContainer<T>(item: T) -> some Container {
    return [item]
}
let opaqueContainer = makeOpaqueContainer(item: 12)
let twelve = opaqueContainer[0]
print(type(of: twelve))
// Prints "Int"
```

`twelve `的类型被推断为`Int`, 这说明类型推断适用于不透明类型这一事实. makeOpaqueContainer(item:)函数实现中,不透明类型的基本类型是`[T]`. 在当前的情况下,`T`是`Int`, 所以返回值是一个整数元素的数组,关联类型`Item`被推断为`Int`.`Container`的脚标返回的是`Item`, 这意味着`twelve`也被推断为`Int`.

