### 环境模型

环境模型与之前介绍过替换模型都属于计算模型，用于描述解释器的运作过程。由于程式中引入了赋值操作，导致替换模型难以正常运作，所以引入新的计算模型，也就是环境模型。

环境模型中的关键元素是 **环境（environments）**，环境由一系列 **框架 （frames）** 构成，每个框架都维护着变量名与变量的绑定关系，当然绑定关系表也可以是空的，不过在每个框架中针对任何变量都只能有一组绑定关系。同时框架为了能够作用于全局会指向自己的闭包环境（也就是外围环境）。在这种情况下，变量的值是指从当前运行环境中的框架开始查找，如果此环境的框架中不存在相应的变量关系，则沿着闭包环境指向不断进行查找，以此方式查找到的第一个含有指定变量的框架中绑定的数值。如果所有框架都不存在指定变量，则称此变量在环境中没有被绑定。

要应用环境模型分析程式的计算过程需要明确解析程式定义和解析程式应用两部分规则。

#### 程式定义

通过 `define` 定义程式时，会产生程式代码和环境指针，程式代码来自  λ 表达式的内容，环境指针指向  λ 表达式产生程式的环境。所以 `define` 定义程式只是  λ 表达式的语法糖，而下列程式也可以改写成  λ 形式。

```scheme
(define (square x)
  (* x x))
```

改为  λ 表达式为

```scheme
(define square
  (lambda (x) (* x x)))
```

上述定义程式的过程发生在哪个环境 `square` 与程式对象的绑定关系就维护在哪个环境。下图展示了在全局环境中定义 `square` 程式后的环境结构。

![Environment structure produced by evaluating (define (square x) (* x x)) in the global environment](https://github.com/CloneableX/SICP-learning/wiki/images/3/3-2.png)

#### 程式应用

程式应用需要满足两条规则：

-   向一组参数应用程式对象就是在构建一个框架，框架中存储了程式形参与应用参数的绑定关系，接着在被创建的新环境中运算程式体，并且新创建的框架所处环境的闭包环境是程式对象指向的环境。
-   由 λ 表达式计算产生的程式与指定环境关联。其中程式对象的结果由 λ 表达式文本和指向程式被创建时环境的指针组成。

例如，有如下程式

```scheme
(define (square x)
  (* x x))
(define (sum-of-squares x y)
  (+ (square x) (square y)))
(define (f a)
  (sum-of-squares (+ a 1) (* a 2)))
```

上述程式定义产生的环境结构如下图

![Procedure objects in the global frame](https://github.com/CloneableX/SICP-learning/wiki/images/3/3-4.png)

使用环境模型分析 `(f 5)` 的运算过程，如下图

![Environments created by evaluating (f 5)](https://github.com/CloneableX/SICP-learning/wiki/images/3/3-5.png)