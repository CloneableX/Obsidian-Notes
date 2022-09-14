#### delay 与 force

`delay` 需要包装一个程式，并在之后按需执行它，其实可以简单地将表达式处理成程式体实现。所以 `(delay <exp>)` 相当于 `(lambda () <exp>)` 的语法糖。`force` 能够调用由 `delay` 产生的程式，所以可以按如下方式实现 `force`。

```scheme
(define (force delayed-object) (delayed-object))
```

在许多应用中，需要对同一个延迟对象进行多次调用，这将导致流中的递归程序效率降低。所以需要使延迟对象只在第一次执行时被构建，然后再将计算结果进行存储。后续对同一延迟对象的强制执行不再重复计算而是返回存储的结果。缓存操作实现如下。

```scheme
(define (memo-proc proc)
  (let ((already-run? false) (result false))
    (lambda ()
      (if (not already-run?)
          (begin (set! result (proc))
                 (set! already-run? true)
                 result)
           result))))
```

`delay` 的实现改写为 `(memo-proc (lambda () <exp>))`。