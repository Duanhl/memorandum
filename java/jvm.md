## openjdk9源码结构
----
> [HotSpot的源码应该怎样阅读](https://hllvm-group.iteye.com/group/topic/26998)

 hotspot src 
* cpu  cpu相关代码
* jdk.aot
* jdk.hotspot.agent
* jdk.internal.vm.ci
* jdk.internal.vm.compile
* os  操作系统相关代码
* os_cpu  操作系统和cpu相关代码
* share  平台无关公用代码
    * tools  工具
        * hsdis  反汇编插件
        * IdealGraphVisualizer  将Server编译器的中间代码可视化工具
        * LogCompilation -XX:+LogCompilation将hotspot.log整理工具
    * vm  vm核心代码
        * adlc  平台描述文件(cpu或os_cpu的*.ad)编译器
        * aot  
        * asm  汇编器接口
        * c1  client编译器
        * ci  动态编译器的公共服务接口
        * classfile  类文件的处理(包括类加载和系统符号表等)
        * code  动态生成的代码管理
        * compliler  编译器接口
        * gc  GC的实现
        * interpreter  解释器，包括模板解释器和C++解释器
        * jvmci  
        * libadt  一些抽象数据结构
        * logging  日志相关
        * memory  内存管理相关(老的分代式GC框架也在这里)
        * oops  HotSpot VM的对象系统的实现  
        * opto  Server编译器
        * precompiled  
        * prims  HotSpot VM的对外接口，包括部分标准库native部分和 JVMTI实现
        * runtime  运行时支持库(包括线程管理、编译器调度、锁、反射等)
        * services  支持JMX之类管理功能接口
        * shark  基于LLVM的JIT编译器
        * trace  
        * utilities  基本工具类


## 虚拟机结构
----
> jvm运行时数据区占用内存 = 堆空间 + (虚拟机栈空间 + 本地方法栈) * 线程数量

## GC
----


## JIT(Just-In-Time) 即时编译
----
一部分代码是解释运行，另一部分是编译后运行


## 逃逸分析
> 如果对象没有其他可以引用的地方，jvm可以针对代码做非常激进的优化