### 元循环求值器

求值器也就是编程语言的解释器，如果求值器是由它要运算的语言缩写就称为 **元循环求值器（metacircular evaluator）**。

求值器通过 `eval` 进行表达式语法分析然后传递给 `apply` 运行，通过两个程式的循环推动使求值器得到运转。所以，对于求值器表达式就是按规则排序的列表，也就完成了程序向数据的转化。除此之外，求值器运算表达式需要建立在环境模型的基础上，可以将环境构建成包含框架和闭包环境的序对，框架是一个包含变量列表与变量值列表（两个列表的对应关系按顺序排列）序对。拥有上述元素后，求值器只需要通过读取表达式-运算表达式-打印结果的循环运行即可。

不过这样构建的求值器会存在两个问题，在内部定义程式时它会根据程式引用情况注册在对应环境中，而不是程式定义的环境中，所以在程式运算时需要将内部程式转化为 `let` 表达式，再使用 `set!` 进行赋值操作。另一个问题是重复语法分析的问题，当表达式被重复调用时其实语法并没有变化，却需要重复进行语法分析，这将导致求值器整体效率低下，所以需要将语法分析与程式求值分离，其中语法分析的结果可以保存以提高求值器运算效率。