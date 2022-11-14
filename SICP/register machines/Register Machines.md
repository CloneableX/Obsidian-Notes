### 寄存器机器

我们通过求值器解释了编程语言运算的细节，但由于之前讲解的求值器都是基于 Lisp 语言开发，所以自然继承 Lisp 的控制结构，于是在求值器部分就没有谈到更加底层的控制结构知识。

计算机中主要将 **寄存器（register）** 作为存储单元，通过 **指令（instruction）** 维护其中的存储内容，这样的计算机也称为寄存器机器。这便是编程语言的底层基础。我们需要通过一门机器语言描述寄存器机器的构造，主要是其中的 **数据通路（data-path）** 和 **控制器（controller）** 结构，虽然可以使用如下类似电子原件电路图的方式描述，但我们希望将其转换为文本形式，通过指令序列的方式描述该结构。

![data path](https://github.com/CloneableX/SICP-learning/wiki/images/5/5-1.png)

![controller](https://github.com/CloneableX/SICP-learning/wiki/images/5/5-2.png)

要实现一台 Lisp 求值器的寄存器机器需要掌握和利用下列技术

- [[Register Machines Description|寄存器机器的描述]]
- [[Storage Allocation|存储管理]]
- [[Garbage Collection|垃圾回收]]

如果将[元循环求值器](obsidian://open?vault=SICP&file=evaluator%2FMetacircular%20Evaluator) 转换为寄存器机器描述，则将得到[[Explicit-Control Evaluator|显示控制求值器]]
