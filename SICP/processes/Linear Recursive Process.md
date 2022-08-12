### 线性递归

我们使用替换模型分析时可能会发现，有些程式的过程会先展开再收敛，通常这种过程就是递归过程，又因为该过程的时间增长趋势为 `O(n)`。所以称为线性递归过程，其空间增长趋势也为 `O(n)`。比如，下列计算阶乘的程式为线性递归过程：

```scheme
(define (factorial n)
  (if (= n 1)
    1
    (* n (factorial (- n 1)))))
```

解析后的计算过程如下图：

![recursive process](https://github.com/Artemis-co-op/SICP/wiki/images/1/1-3.png)