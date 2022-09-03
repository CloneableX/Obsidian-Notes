### 序列

序对能够构建的重要数据结构中有一种叫做 **序列（sequence）**，它是一组有序数据对象。

#### 列表与树形结构

通过上述关于序对知识点的了解，如果使用 `cons` 嵌套构建将形成一组有序数据对象列表，它是序列数据结构的一种构建形式。除了 `cons` 嵌套的方式外，Scheme 提供了基础程式 `list` 构建列表，也就是说下列两个结构完全相等：

```scheme
(list <a1> <a2> ... <an>)

(cons <a1>
	  (cons <a2>
	         (cons ...
	                (cons <an> nil))))
```

如果对列表进行嵌套构建，将形成树形结构，例如 `(list (list 1 2) (list 3 4))` ，树形结构的运算是递归的，不过也可以将叶子节点转换为列表，便于统一使用列表的操作。

在列表表达方式统一的情况下，可以为其抽象 `map`、`filter`  和 `accumulate` 等运算程式，相当于为列表建立抽象屏障，这样做有利于避免使用列表时需要关注它的具体实现，为底层实现的修改在整个程序系统上的统一性打下良好基础。

#### 队列

在[序对赋值基础程式](obsidian://open?vault=SICP&file=procedures%2FSpecial%20Forms) `set-car!` 和 `set-cdr!` 的基础上，我们可以实现队列结构。它是一种序列，这种序列只允许从一端添加数据（称为队列的后端）从另一端删除数据（称为队列的前端），也称为 FIFO（先进先出）缓存。

具体实现可以查看 [队列的实现](https://github.com/CloneableX/SICP-learning/wiki/3.3-Modeling-with-Mutable-Data#%E6%9E%84%E5%BB%BA%E9%98%9F%E5%88%97)。

#### 表

在[数据导向编程](obsidian://open?vault=SICP&file=methodology%26property%2FData-Directed%20Programming)中使用 `get` 和 `put` 程式将代码包的接口注册在系统的类型-操作对应表中，在[序对赋值基础程式](obsidian://open?vault=SICP&file=procedures%2FSpecial%20Forms) `set-car!` 和 `set-cdr!` 的基础上也表数据的插入便成为可能。不过类型-操作对应表为二维表，具体实现可以查看[表的实现](https://github.com/CloneableX/SICP-learning/wiki/3.3-Modeling-with-Mutable-Data#%E6%9E%84%E5%BB%BA%E8%A1%A8)
