#### 赋值操作

`set!` 程式是一种特殊形式的表达式，语法为 `(set! <name> <new-value>)`，`set!` 会将 `<name>` 对应的值修改为 `<new-value>` 的运算结果

#### 序对赋值操作

除了普通数据对象可以使用赋值操作外，序对也有相应的赋值操作程式，分别是 `set-car!` 和 `set-cdr!` ，能够对序对的 `car` 对应值和 `cdr` 对应值进行修改。但是序对实现方式导致会存在序对共享的情况，对某个序对进行赋值操作将影响共享使用此序对的其他对象，所以对序对进行赋值操作时需要特别小心。

比如下列程式

```scheme
(define x (list 'a 'b))
(define z1 (cons x x))
```

其内部链接结构如下图

![The list z1 formed by (cons x x)](https://github.com/CloneableX/SICP-learning/wiki/images/3/3-16.png)

所以只要对 x 的元素进行赋值操作 z1 的 `car` 值和 `cdr` 值都将受到影响

在拥有了序对赋值操作后，序对的应用将得到很大扩展，比如用于实现队列、表等数据结构。
