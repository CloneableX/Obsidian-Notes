#### 序对程式

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