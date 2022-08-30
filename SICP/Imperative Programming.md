### 命令式编程

函数式编程中的同一程式接收相同参数，得到的结果将完全一致，这种能够将程式视为数学函数表达的编程风格便是函数式编程。

一旦在函数式编程的基础上引入了赋值操作，编程风格就转变为 **命令式编程（imperative programming）**。引入赋值操作便可以为计算模型对象维护状态变量，使对象本身的行为根据状态变量值的变化而变化。具体例子如下：

```scheme
(define balance 100)
(define (withdraw amount)
  (if (>= balance amount)
      (begin (set! balance (- balance amount))
             balance)
      "Insufficient funds"))
```

上述例子的状态变量被定义在全局环境中，将其移到 `withdraw` 内部更有利于模块化，可改写如下：

```scheme
(define new-withdraw
  (let ((balance 100))
    (lambda (amount)
      (if (>= balance amount)
          (begin (set! balance (- balance amount))
                 balance)
          "Insufficient funds"))))
```

或者也可以将程式的参数作为局部状态变量维护

```scheme
(define (make-withdraw balance)
  (lambda (amount)
    (if (>= balance amount)
        (begin (set! balance (- balance amount))
               balnace)
        "Insufficient funds")))
```

#### 命令式编程的优点和代价

虽然命令式编程带来了更强大的模块化设计技术，极大降低计算模型对象之间的耦合，但也导致它的运作过程无法通过替换模型解析。除此之外，赋值导致的透明引用失效和对象的同一性问题也将暴露无疑。

因为赋值操作的引入，使用相同方式相同参数创建的两个对象因为调用频次等的不同，很可能产生不同的结果。也就是说，对于两个同样过程创建的对象，在同一计算过程中相互替换，并不能保证对计算结果没有影响。其实也就是 **透明引用（referentially transparent）** 的失效，而函数式编程是满足透明引用的。

其次，如果要对比两个对象是否相同，只能通过改变其中一个对象，然后观测另一个对象是否发生同样的变化确定。但我们无法明确知晓哪个对象变化，从而避免两次观察的是同一对象；另外，对比两个对象的变化也不知道具体要对比其中的哪些状态变量。这导致对象的同一性判断难以进行。

最后，命令式编程中的赋值通常是有顺序区分的，当几个状态变量之间的赋值顺序发生变化将产生错误的结果，对于函数式编程同样也不存在这样的问题。