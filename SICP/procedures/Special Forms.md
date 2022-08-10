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

#### lambda 表达式

`lambda` 表达式与 `define` 作用类似，可以将表达式抽象为程式，不过无法命名，结构体如下

```scheme
(lambda (<formal-parameters>) <body>)
```

`lambda` 表达式同样可以作为表达式的操作符使用，如下：

```scheme
((lambda (x y z) (+ x y (sqaure z)))
   1 2 3)
```

##### let 表达式
`let` 用于定义局部变量，结构体如下：

```scheme
(let ((<var1> <exp1>)
      (<var2> <exp2>)
      ...
      (<varn> <expn>))
  <body>)
```

其实 `let` 并不是一种表达式的特殊形式，而是基于 `lambda` 的语法糖，`let` 表达式的结构体转换为 `lambda` 形式如下：

```scheme
((lambda (<var1> <var2> ... <varn>) 
   <body>)
  <exp1>
  <exp2>
  ...
  <expn>)
```

`let` 定义的局部变量作用域为 `let` 表达式体中，于是衍生出下列两条规则：

- `let` 仅允许被绑定的变量在此 `let` 表达式体中应用。例如，假设下列表达式中的 x 值为 5，整个表达式的结果为 38。因为在 `let` 体中应用的 x 值为 3，所以 `let` 表达式的结果为 33，而 `let` 表达式外部的 x 依然是 5，所以整个表达式的结果为 38。

```scheme
(+ (let ((x 3))
     (+ x (* x 10)))
   x)
```

-  `let` 中局部变量值的计算依靠 `let` 外部的变量。也就是说，`let` 中存在与表达式外部同名的变量，在计算 `let` 中局部变量结果时使用的依然是外部变量。例如，假设下列表达式中 x 的值是 2，整个表达式的结果为 12。因为 `let` 表达式中 x 值为 3，y 的值为 4（计算 y 时使用的 x 值为 `let` 外部的 x 值 2）。

```scheme
(let ((x 3)
      (y (+ x 2)))
  (* x y))
```


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

#### 序对程式

^6d4e39

Scheme 通过序对的方式为我们提供数据抽象的能力，分别通过程式 `cons`、`car` 和 `cdr`。`cons` 接收两个参数，并将这两个参数绑定返回一个数据对象；`car` 可获取数据对象的前一个参数并作为结果返回，`cdr` 可获取数据对象的后一个参数并作为结果返回。具体使用方式如下：

```scheme
(define x (cons 1 2))
(car x)
1
(cdr x)
2
```

当然 `cons` 的参数也可以是数据对象，例如：

```scheme
(define x (cons 1 2))
(define y (cons 3 4))
(define z (cons x y))
(car (car z))
1
(car (cdr z))
3
```