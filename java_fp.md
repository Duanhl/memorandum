## 标准函数库
----
> package java.util.function

| Interface | Function Signature | Example|
|----|----|----|
|UnaryOperator<T> |T apply(T t) |String::toLowerCase|
|BinaryOperator<T> |T apply(T t1, T t2)| BigInteger::add|
|Predicate<T> |boolean test(T t) |Collection::isEmpty|
|Function<T,R> |R apply(T t) |Arrays::asList|
|Supplier<T> |T get() |Instant::now|
|Consumer<T> |void accept(T t) |System.out::println|

### 值类型函数

> java泛型不支持值类型，在大量使用函数式编程的场景下，比如大批量数据处理，值类型的装箱开箱开销不可忽略，所以引入了这种基本值类型的函数。

命名方式可以分按参数类型、按返回类型、参数和返回类型相结合。
| Nomenclature | Example Interface | Function Signature|
|----|----|----|
|Parameter Type |IntFunction<R> |R apply(int value)|
| |ObjDoubleConsumer<T>|void accept(T t, double value)|
|Return Type|ToDoubleFunction<T>|double applyAsDouble(T value)|
| | DoubleSupplier| double getAsDouble()|
|Parameter And Return |LongToDoubleFunction |double applyAsDouble(long value)|
| |LongUnaryOperator |long applyAsLong(long operand)|


## 方法引用
----
在jdk8以前，如果方法参数需要某个接口类型而这个实现只会在这里使用，可以使用匿名内部内形式来实现该接口并创建一个这样的实例。

* anonymous inner class
```
    private <T, R> R testFunction(T t, Function<T, R> function){
        return function.apply(t);
    }

    public void test(){
        testFunction("Hello", new Function<String, String>() {
            @Override
            public String apply(String s) {
                return s.toLowerCase();
            }
        });
    }
```

如果用lambda表达式，上面的匿名内部类可以写成这样。
* lambda
```
    public void test(){
        testFunction("Hello", str -> str.toLowerCase());
    }
```

还可以更使用更简洁的方法引用
* method reference
```
    public void test(){
        testFunction("Hello", String::toLowerCase);
    }
```

### 方法引用的类型
| Type | Example|
|----|----|
|ExpressionName :: [TypeArguments] Identifier |T apply(T t) |
|Primary :: [TypeArguments] Identifier |System.out::println| 
|ReferenceType :: [TypeArguments] Identifier|String::valueOf |
|super :: [TypeArguments] Identifier |super::toString |
|ClassType :: [TypeArguments] new |StringBuild::new |
|ArrayType :: new |int[]::new|


## 错误处理

<pre>
error code:

    try {
        Stream.of("hello", "world").forEach(str -> str.getBytes("UTF-8"));
    } catch (UnsupportedEncodingException e){

    }

</pre>

<pre>
ungraceful practices:

    stream.forEach((str) -> {
        try {
            this.throwExceptionMethod(str);
        } catch (Exception e){
            
        }
    });
</pre>

<pre>

</pre>

