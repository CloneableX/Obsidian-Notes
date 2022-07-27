编程语言应该具备将简单的概念组合为复杂概念的能力，这个能力与三种机制相关

- 基础表达式
- 组合
- 抽象

### 组合基本元素

`+`、`-`、`*`、`/`  及数字等预先定义在解释器中的都是基本元素，在 Lisp 中通过小括号的方式将这些基本元素组合起来，例如：

`(+ 1 10)`
`(/ 6 3)`

分别由位于最左侧的操作符与其他元素，也就是操作数组成，其中操作数会被解释器解释为计算机机器指令，再将操作数应用于此机器指令。

Lisp 表达式通过前缀表达式（也就是将操作符置于表达式最左侧）的方式表述，这有利于参数的数量灵活增减，同样也利于嵌套表达式的编写，例如：

`(+ (- 10 1) (* 2 4))`

### 表达式的特殊形式

除了上述常见的表达式之外，还存在情况比较特殊的表达式，如 `define`、`cond` 和 `if` 等，这些表达式并不是按照统一的表达式规则运算，而是各有规则。

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

#### 条件表达式

`cond` 表达式的结构如下：

```scheme
(cond (<p1> <e1>)
	  (<p2> <e2>)
	  (<p3> <e3>))

(define (abs x)
  (cond ((> x 0) x)
        ((= x 0) 0)
        ((< x 0) (- x))))
```

p 表示逻辑判断的表达式，结果为 true 时会执行对应的 e，也就是结果表达式，并将计算结果返回，如果 p 的结果为 false，则按此规则执行下一条用例。如果用例只有两条时也可如下编写：

```scheme
(cond (<p1> <e1>)
	  (else <e2>))

(define (abs x)
  (cond ((< x 0) (- x))
        (else x)))
```

虽然 `cond` 不是表达式而是特殊形式，但 `else` 是表达式，因为 `else` 会应用所有操作数。上述结构也可以用 `if` 特殊形式改写：

```scheme
(if <predicate> <consequent> <alternative>)

(define (abs x)
  (if (< x 0)
      (- x)
      x))
```

除此之外还可以使用 `and` 、`or` 和 `not` 这些复合条件表达式，如下：

```scheme
(and <e1> <e2> ... <en>)
(or <e1> <e2> ... <en>)
(not <e>)
```

这几种条件表达式除了 `not` 外都是特殊形式，`and` 和 `or` 都符合开关原则，不过 `and` 所有表达式结果都为 true 时最终返回最后一个表达式的运算结果，而 `or` 在所有表达式都为 false 时最终只返回 false。

### 替换模型

模型的目的是帮助我们思考程式是如何被运算的，而不是在描述解释器如何运作，随着对解释器的深入了解模型也会遇到问题，此时就需要寻找更加合适的模型帮助我们思考。

替换模型会将程式的形参替换为对应的数值，并计算表达式结果。模型也存在不同的运算方式，分别是 **normal-order evaluation** 和 **applicative-evaluation**。

#### applicative-evaluation

有如下程式：

```scheme
(define (sum-of-squares x y)
  (+ (square x) (square y)))

(define (square x) (* x x))

(define (f x)
  (sum-of-squares (+ x 1) (* x 2)))
```

通过 applicative-evaluation 的方式展开表达式 `(f 5)`。

```scheme
(sum-of-squares (+ 5 1) (* 5 2))
(sum-of-squares 6 10)
(+ (square 6) (square 10))
(+ (* 6 6) (* 10 10))
(+ 36 100)
136
```

applicative-evaluation 会将操作数计算得到结果再应用。

#### normal-order evaluation

继续使用 applicative-order evaluation 的程式作为例子，通过 normal-order evaluation 的方式展开表达式 `(f 5)`

```scheme
(sum-of-squares (+ 5 1) (* 5 2)))
(+ (square (+ 5 1)) (square (* 5 2)))
(+ (* (+ 5 1) (+ 5 1)) (* (* 5 2) (* 5 2)))

(+ (* 6 6) (* 10 10))
(+ 36 100)
136
```

normal-order evaluation 会将表达式完全展开后再应用，你会发现有些表达式在收敛时被重复计算，例如 `(+ 5 1)`。这也是解释器使用 applicative-order evaluation 的原因之一，可以在计算时提高效率，另一个原因是当后期出现无法使用替换模型无法解释的情况时，使用 normal-order evaluation 会遇到更多问题。

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