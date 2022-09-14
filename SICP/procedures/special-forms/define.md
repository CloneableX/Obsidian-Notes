#### define

`define` 有主要有两种使用场景，绑定参数和定义程式，例如：

`(define size 3)`

是将 3 与 名为 size 变量或对象绑定，在同一环境中使用 size 时输出的结果就是 3，而定义程式的情况如下：

`(define (<name> <formal parameters>) <body>)` 

```scheme
(define (square x)
  (* x x))
```

通过 `define` 可以将一个名字与一个基本元素或一段表达式关联，这也是 Lisp 这门编程语言抽象能力的体现。