## Kotlin系列之let,with,run,apply,also函数使用

### 内联扩展函数let
let函数实际上是一个作用域函数，用处一般有二
- 当需要定义一个变量在一个特定作用域
- 可以用来避免一些null的判断操作

eg.
```
object.let{
it.todo()//在函数体内使用it替代object对象去访问其公有的属性和方法
...
}

//另一种用途 判断object为null的操作
object?.let{//表示object不为null的条件下，才会去执行let函数体
it.todo()
}
```
函数返回最后一行或者指定return的表达式
### 内联函数之with
with函数是不是一个扩展函数，是内联函数，将对象作为参数，在函数块内可以通过this指代该对象，返回值为最后一行或指定return的表达式
eg.
```
val result = with(user) {
        println("my name is $name, I am $age years old, my phone number is $phoneNum")
        1000
    }
```
好处是函数内部不用再写对象名，直接可以调用该对象的方法
### 内联扩展函数之run

run相当于是let和with的结合体，返回最后一行或者return的表达式
eg.
```
 val result = user.run {
        println("my name is $name, I am $age years old, my phone number is $phoneNum")
        1000
    }
    println("result: $result")
```
### 内联扩展函数之apply

apply与run类似，只不过返回的是**对象本身**，一般可用于对象初始化之后，对对象属性进行一些赋值操作

eg.
```
mSheetDialogView = View.inflate(activity, R.layout.biz_exam_plan_layout_sheet_inner, null).apply{
   course_comment_tv_label.paint.isFakeBoldText = true
   course_comment_tv_score.paint.isFakeBoldText = true
   course_comment_tv_cancel.paint.isFakeBoldText = true
   course_comment_tv_confirm.paint.isFakeBoldText = true
   course_comment_seek_bar.max = 10
   course_comment_seek_bar.progress = 0

}
```
### 内联扩展函数之also
also与let类似，不同在于返回对象本身


