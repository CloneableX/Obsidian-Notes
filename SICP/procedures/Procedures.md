我们可以简单地通过使用 `define` 将[[Expressions|表达式]]定义为程式，除些之外，还可以通过将程式作为参数或返回结果构建[[High-Order Procedures|高阶程式]]。

### 程式的黑盒抽象

当一个程式被使用时它应该如同黑盒一般，使用者不需要关注其中的实现细节，如同 `square` 程式。所以从黑盒抽象的层次来说会忽略程式的实现细节，而形参名称也会被忽略（是指不同的开通名称对程式的运算结果无影响）。

并且形参属于绑定变量声明，它的作用域只有对应的程式体内，只要程式内部对形参统一修改名称对程式的结果并无影响。

不过关于黑盒的抽象还有个问题需要解决，如果像下述例子一样定义程式，其他的程式想定义一个自己的 `sum-of-squares` ，而不影响程式 `f` 的结果便无法实现，使用者不得不了解其中实现的细节。

```scheme
(define (sum-of-squares x y)
  (+ (square x) (square y)))

(define (square x) (* x x))

(define (f x)
  (sum-of-squares (+ x 1) (* x 2)))
```

不过我们可以通过代码块结构进入程式的内部定义，如下：

```scheme
(define (f x)
  (define (sum-of-squares x y)
    (+ (square x) (square y)))
  (define (square x)
    (* x x))
  (sum-of-squares (+ x 1) (* x 2)))
```

这种方式既有效地将大型程序切割为易维护的代码片段，又解决了包命名的问题。