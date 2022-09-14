#### 流的操作

- `(cons-stream x y)`：流的构造器，与表达式 `(cons x (delay y))` 等价
- `(stream-car <stream>)`：流的选择器，与表达式 `(car <stream>)` 等价
- `(stream-cdr <stream>)`：流的选择器，与表达式 `(force (cdr <stream>))` 等价
- `the-empty-stream`： 一个特殊对象，对其应用 `cons-stream` 不会返回任何结果，不过可以通过 `stream-null?` 判断

流的实现将基于特殊形式 `delay`。`(delay <exp>)` 并不会计算表达式 `<exp>`，而是返回 **延迟对象（delay object）**，可以将其视作能够在未来时间进行 `<exp>` 运算的承诺。经常伴随 `delay` 出现的程式 `force` 能够接收延迟对象作为参数并执行其中的表达式，实际上是要求 `delay` 实现它的承诺。