### 消息传递

[数据导向编程](obsidian://open?vault=SICP&file=methodology%26property%2FData-Directed%20Programming)的关键在于将通用程式的处理对象转变为了操作与类型对应表。这种编程风格主要用于处理需要按类型分发调用对应程式的情况，实际上是通过将操作类型对应表中的数据分成一行一行地处理，因为表格的每行都表示通用操作程式。

另一种策略是通过将表格按列分解的方式处理，按类型分发替换为集成操作，按操作名称分发替换为集成数据对象。按照这种方式能够将相关的事情集中到同一数据对象中，对于矩形数据，就通过接收操作名称的程式和操作对应的程式实现。在这种情况下，`make-from-real-imag` 可以改写为：

```scheme
(define (make-from-real-imag x y)
  (define (dispatch op)
    (cond ((eq? op 'real-part) x)
          ((eq? op 'imag-part) y)
          ((eq? op 'magnitude) (sqrt (+ (square x) (square y))))
          ((eq? op 'angle) (atan y x))
          (else (error "Unknown op: MAKE-FROM-REAL-IMAG" op))))
  dispatch)
```

相应地，`apply-generic` 程式只需要提供操作名称给数据对象，数据对象便可以正常运作。

`(define (apply-generic op arg) (arg op))`

要注意的是 `make-from-real-imag` 返回的结果是一个内部程式 `dispatch`，这个程式在 `apply-generic` 需要调用具体程式时使用。

这种编程风格称为消息传递。当一个数据对象被视作接收请求的实体，请求本身是操作名称时，此时整个系统就如同消息传递一般。刚才提及的消息传递并不是一个数学技巧，但它是一项在通过通用操作组织系统上十分有用的技术。