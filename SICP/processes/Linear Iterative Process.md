### 线性迭代

线性迭代也是通过替换模型解析程式时生成，此类过程即不会展开也不会收敛，但在每一步都会保存当前步骤的状态数据，依靠状态数据的迭代产生下一步计算。也因为该过程的时间增长趋势 `O(n)`，所以又称为线性迭代过程。

虽然线性迭代过程的时间增长趋势与线性递归过程一样，但线性迭代过程空间增长趋势为 `O(1)
，并且可以通过当前步骤存储的状态数据随时重启程序。

例如，下列计算阶乘的程式为线性迭代过程：

```scheme
(define (factorial n)
  (fact-iter 1 1 n))

(define (fact-iter product counter max-count)
  (if (> counter max-count)
      product
      (fact-iter (* product counter)
                 (+ counter 1)
                 max-count)))
```

解析后的计算过程如下图：

![iterative process](https://github.com/Artemis-co-op/SICP/wiki/images/1/1-4.png)
