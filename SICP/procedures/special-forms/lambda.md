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