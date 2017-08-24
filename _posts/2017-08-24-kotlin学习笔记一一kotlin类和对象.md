# kotlin学习笔记一一kotlin类和对象

##构造函数
kotlin一个类中可以有一个主构造函数和多个次构造函数。主构造函数跟在类头后面

如果主构造函数没有任何注解或者可见性修饰符，可以省略constructor关键字

- 主构造函数不能放任何代码
- 初始化操作放在init关键字为前缀的初始化块中
- 主构造参数可以在init块里使用，也可以在类属性里使用

如果类有一个主构造函数，每个次构造函数需要委托给主构造函数， 可以直接委托或者通过别的次构造函数间接委托。委托到同一个类的另一个构造函数用 this 关键字即可：

```
class Person(val name: String) {
    constructor(name: String, parent: Person) : this(name) {
        parent.children.add(this)
    }
}
```

## 继承
在 Kotlin 中所有类都有一个共同的超类 Any，这对于没有超类型声明的类是默认超类：

```
class Example // 从 Any 隐式继承
```

Any 不是 java.lang.Object；尤其是，它除了 equals()、hashCode()和toString()外没有任何成员。
要声明一个显式的超类型，我们把类型放到类头的冒号之后：

```
open class Base(p: Int)

class Derived(p: Int) : Base(p)
```
如果该类有一个主构造函数，其基类型可以（**并且必须**） 用（**基类型的**）主构造函数参数就地初始化。
如果类没有主构造函数，那么每个次构造函数必须使用 super 关键字初始化其基类型，或委托给另一个构造函数做到这一点。 注意，在这种情况下，不同的次构造函数可以调用基类型的不同的构造函数：

```
class MyView : View {
    constructor(ctx: Context) : super(ctx)

    constructor(ctx: Context, attrs: AttributeSet) : super(ctx, attrs)
}
```

### 覆盖方法

我们之前提到过，Kotlin 力求清晰显式。与 Java 不同，Kotlin 需要显式标注可覆盖的成员（我们称之为开放）和覆盖后的成员：

```
open class Base {
    open fun v() {}
    fun nv() {}
}
class Derived() : Base() {
    override fun v() {}
}
```
Derived.v() 函数上必须加上 override标注。如果没写，编译器将会报错。 如果函数没有标注 open 如 Base.nv()，则子类中不允许定义相同签名的函数， 不论加不加 override。在一个 final 类中（没有用 open 标注的类），开放成员是禁止的。
标记为 override 的成员本身是开放的，也就是说，它可以在子类中覆盖。如果你想禁止再次覆盖，使用 final 关键字：

```
open class AnotherDerived() : Base() {
    final override fun v() {}
}
```
### 覆盖属性
属性覆盖与方法覆盖类似；在超类中声明然后在派生类中重新声明的属性必须以 override 开头，并且它们必须具有兼容的类型。每个声明的属性可以由具有初始化器的属性或者具有 getter 方法的属性覆盖。

```
open class Foo {
    open val x: Int get() { …… }
}

class Bar1 : Foo() {
    override val x: Int = ……
}
```

你也可以用一个 var 属性覆盖一个 val 属性，但反之则不行。这是允许的，因为一个 val 属性本质上声明了一个 getter 方法，而将其覆盖为 var 只是在子类中额外声明一个 setter 方法。
请注意，你可以在主构造函数中使用 override 关键字作为属性声明的一部分。

```
interface Foo {
    val count: Int
}

class Bar1(override val count: Int) : Foo

class Bar2 : Foo {
    override var count: Int = 0
}
```
### 调用超类的实现
派生类中的代码可以使用 super 关键字调用其超类的函数与属性访问器的实现：

```
open class Foo {
    open fun f() { println("Foo.f()") }
    open val x: Int get() = 1
}

class Bar : Foo() {
    override fun f() { 
        super.f()
        println("Bar.f()") 
    }
    
    override val x: Int get() = super.x + 1
}
```
在一个内部类中访问外部类的超类，可以通过由外部类名限定的 super 关键字来实现：super@Outer：

```
class Bar : Foo() {
    override fun f() { /* …… */ }
    override val x: Int get() = 0
    
    inner class Baz {
        fun g() {
            super@Bar.f() // 调用 Foo 实现的 f()
            println(super@Bar.x) // 使用 Foo 实现的 x 的 getter
        }
    }
}
```

### 覆盖规则
在 Kotlin 中，实现继承由下述规则规定：如果一个类从它的直接超类继承相同成员的多个实现， 它必须覆盖这个成员并提供其自己的实现（也许用继承来的其中之一）。 为了表示采用从哪个超类型继承的实现，我们使用由尖括号中超类型名限定的 super，如`super<Base>`：

```
open class A {
    open fun f() { print("A") }
    fun a() { print("a") }
}

interface B {
    fun f() { print("B") } // 接口成员默认就是“open”的
    fun b() { print("b") }
}

class C() : A(), B {
    // 编译器要求覆盖 f()：
    override fun f() {
        super<A>.f() // 调用 A.f()
        super<B>.f() // 调用 B.f()
  }
}
```
## 抽象类
类和其中的某些成员可以声明为 abstract。 抽象成员在本类中可以不用实现。 需要注意的是，我们并不需要用 open 标注一个抽象类或者函数——因为这不言而喻。
我们可以用一个抽象成员覆盖一个非抽象的开放成员

```
open class Base {
    open fun f() {}
}

abstract class Derived : Base() {
    override abstract fun f()
}
```
## 伴生对象

类内部的对象声明可以用 companion 关键字标记：

```
class MyClass {
    companion object Factory {
        fun create(): MyClass = MyClass()
    }
}
```
该伴生对象的成员可通过只使用类名作为限定符来调用：

```
val instance = MyClass.create()
```

可以省略伴生对象的名称，在这种情况下将使用名称 Companion：

```
class MyClass {
    companion object {
    }
}

val x = MyClass.Companion
```




