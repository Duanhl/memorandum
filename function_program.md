
- Referential Transparency(引用透明性)
> 在程序p中的表达式e，e可以直接替换成其返回结果，而不会引起程序p的改变，称之为引用透明
<pre>
    def compute(x : Int) : Int = {
        do other thing  //可能不是引用透明
        x * x   //引用透明
    }
</pre>

引用透明实际上要求expression用返回值来表示函数做了什么，而非是执行什么过程

- Pure Function(纯函数)
> 函数中任意表达式都是引用透明，也就是无副作用(side effict)

> f(x) => y,对于参数x，返回值永远为y