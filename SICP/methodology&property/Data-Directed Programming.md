### 数据导向编程

虽然[类型标识](obsidian://open?vault=SICP&file=methodology%26property%2FType%20Tag)解决了多种表现方式的问题，但它存在两个缺陷，一是通用选择器需要了解所有的类型以及对应类型的程式，二是在不同表现方式独立开发情况下容易出现名字冲突的情况（类型标识中的例子在各自的程式中加入了不同的后缀避免这种情况）。这两个缺陷导致在系统中再增加新的表现形式时需要修改原有的代码，这不仅不方便而且容易导致程序出错，所以我们需要一种更加方便的方式实现数据对象的多种表现，它就是数据导向编程。

数据导向编程将通用程式与不同类型对应程式视为二维表格中的一行数据，查找某个类型的具体程式只需要提供类型和程式名称这两个参数，新增的类型也只需要在表中登记对应的类型和程式名称即可，于是对于类型的判断被转换为了对表格的维护。如下图所示：

![](https://github.com/Artemis-co-op/SICP/wiki/images/2/2-22.png)

在 Scheme 中可以通过 `put` 和 `get` 操作此表格。要顺利地应用数据导向编程技术，除不同的表现形式代码包开发外，还需要实现 `apply-generic` 程式，它能够根据程式名称和数据对象类型确定将要执行的程式，并将当前数据对象应用于目标程式。其他的通用选择器只需要基于 `apply-generic` 实现即可。

```scheme
(define (install-rectangular-package)
  ;; internal procedures
  (define (real-part z) (car z))
  (define (imag-part z) (cdr z))
  (define (make-from-real-imag x y) (cons x y))
  (define (magnitude z)
    (sqrt (+ (square (real-part z))
             (square (imag-part z)))))
  (define (angle z)
    (atan (imag-part z) (real-part z)))
  (define (make-from-mag-ang r a)
    (cons (* r (cos a)) (* r (sin a))))

  ;; interface to the rest of the system
  (define (tag x) (attach-tag 'rectangular x))
  (put 'real-part '(rectangular) real-part)
  (put 'imag-part '(rectangular) imag-part)
  (put 'magnitude '(rectangular) magnitude)
  (put 'angle '(rectangular) angle)
  (put 'make-from-real-imag 'rectangular
    (lambda (x y) (tag (make-from-real-imag x y))))
  (put 'make-from-mag-ang 'rectangular
    (lambda (r a) (tag (make-from-mag-ang r a))))
  'done)

(define (install-polar-package)
  ;; internal procedures
  (define (magnitude z) (car z))
  (define (angle z) (cdr z))
  (define (make-from-mag-ang r a) (cons r a))
  (define (real-part z) (* (magnitude z) (cos (angle z))))
  (define (imag-part z) (* (magnitude z) (sin (angle z))))
  (define (make-from-real-imag x y)
    (cons (sqrt (+ (square x) (square y)))
          (atan y x)))

  ;; interface to the rest of the system
  (define (tag x) (attach-tag 'polar x))
  (put 'real-part '(polar) real-part)
  (put 'imag-part '(polar) imag-part)
  (put 'magnitude '(polar) magnitude)
  (put 'angle '(polar) angle)
  (put 'make-from-real-imag 'polar
       (lambda (x y) (tag (make-from-real-imag x y))))
  (put 'make-from-mag-ang 'polar
       (lambda (r a) (tag (make-from-mag-ang r a))))
  'done)

(define (apply-generic op . args)
  (let ((type-tags (map type-tag args)))
    (let ((proc (get op type-tags)))
      (if proc
          (apply proc (map contents args))
          (error
            "No method for these types: APPLY-GENERIC"
            (list op type-tags))))))

;; generic selectors
(define (real-part z) (apply-generic 'real-part z))
(define (imag-part z) (apply-generic 'imag-part z))
(define (magnitude z) (apply-generic 'magnitude z))
(define (angle z) (apply-generic 'angle z))
```
