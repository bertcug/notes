# PIN #
**Pin 是一种动态二进制检测框架，适用于x86,x64架构，一般用于程序动态分析用，是多个平台都支持，windows 、linux以及OSX。该工具原本适用于计算机架构分析用的，但是由于丰富的api，与强大的功能现在运用的地方很多。比如计算机安全，环境模拟器，并行计算。**

## PIN的插桩层次
* **指令级插桩（instruction instrumentatio）**
    >通过函数`INS_AddInstrumentFunctio`实现
* **轨迹级插装（trace instrumentation)**
    >	通过函数`TRACE_AddInstrumentFunction`实现。（貌似就是基本块插装）
* **镜像级插装（image instrumentation）**
    >使用`IMG_AddInstrumentFunction`函数由于其依赖于符号信息去确定函数边界，因此必须在调用`PIN_Init`之前调用`PIN_InitSymbols`。
* **函数级的插装（routine instrumentation）**
    >使用RTN_AddInstrumentFunction函数。函数级插装比镜像级插装更有效，因为只有镜像中的一小部分函数被执行。

其中，`IMG_AddInstrumentFunction`和`RTN_AddInstrumentFunction`需要先调用`PIN_InitSymbols()`，来分析出符号。在无符号的程序中，`IMG_AddInstrumentFunction`和`RTN_AddInstrumentFunction`无法分析出相应的需要插装的块。

在各种粒度的插装函数调用时，可以添加自己的处理函数在代码中，程序被加载后，在被插装的代码运行时，自己添加的函数会被调用。 

## 搜集到的开源 PIN Tool ##
1. [Use After Free漏洞检测](https://github.com/JonathanSalwan/PinTools/tree/master/ClassicalUseAfterFreePatternMatching)
2. [Format String Buffer Overflow](https://github.com/JonathanSalwan/PinTools/tree/master/FormatStringDetection)
3. [Buffer Overflow](https://github.com/JonathanSalwan/PinTools/tree/master/OverflowDetection)

**此外该作者还基于PIN编写了动态符号化执行和In Memory Fuzzing工具：**

1. [ConcolicExecution](https://github.com/JonathanSalwan/PinTools/tree/master/ConcolicExecution)
2. [InMemoryFuzzing](https://github.com/JonathanSalwan/PinTools/tree/master/InMemoryFuzzing)

## 已完成的工作 ##

* **指定函数的内存操作监控**
![](http://i.imgur.com/loe6ogU.png)