## Kotlin对象表达式和对象声明

### 对象表达式
- 对象可以继承于某个基类，或者实现其他接口:
- 如果超类型有一个构造函数，则必须传递参数给它。多个超类型和接口可以用逗号分隔。
- **对象表达式可以越过类的定义直接得到一个对象&&/&&对象表达中可以方便的访问到作用域中的其他变量**
- 匿名对象可以用作只在本地和私有作用域中声明的类型，如果用在公用的属性类型，则实际的类型回去找对象的超类
```
fun countClicks(window: JComponent) {
    var clickCount = 0
    var enterCount = 0

    window.addMouseListener(object : MouseAdapter() {
        override fun mouseClicked(e: MouseEvent) {
            clickCount++
        }

        override fun mouseEntered(e: MouseEvent) {
            enterCount++
        }
    })
    // ……
}

```
### 对象声明

- 通过对象声明来获得一个单例
- 对象可以有超类型
- 当对象声明在另一个类的内部时，这个对象并不能通过外部类的实例访问到该对象

### 伴生对象

- companion 它就与外部类关联在一起，我们就可以直接通过外部类访问到对象的内部元素。
- 一个类里面只能声明一个内部关联对象，即关键字 companion 只能使用一次。

### 对象表达式和对象声明区别

- 对象表达式是在使用他们的地方立即执行的
- 对象声明是在第一次被访问到时延迟初始化的
- 伴生对象的初始化是在相应的类被加载（解析）时，与 Java 静态初始化器的语义相匹配

