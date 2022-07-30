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
(sum-of-squares (+ 5 1) (* 5 2))
(+ (square (+ 5 1)) (square (* 5 2)))
(+ (* (+ 5 1) (+ 5 1)) (* (* 5 2) (* 5 2)))

(+ (* 6 6) (* 10 10))
(+ 36 100)
136
```

normal-order evaluation 会将表达式完全展开后再应用，你会发现有些表达式在收敛时被重复计算，例如 `(+ 5 1)`。这也是解释器使用 applicative-order evaluation 的原因之一，可以在计算时提高效率，另一个原因是当后期出现无法使用替换模型无法解释的情况时，使用 normal-order evaluation 会遇到更多问题。