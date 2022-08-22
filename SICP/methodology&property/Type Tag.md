### 类型标识

数据抽象除了单一类型的情况外，还存在多类型的数据抽象，这种情况下需要对不同类型的数据对象表现形式采取通用程式的手段。通用程式可以根据数据对象的具体表现形式将其应用于对应的程式，如何判断数据对象的表现形式呢？可以通过类型标识技术，也就是使标识成为原始数据对象的一部分，在通用程式中便可以从传入的数据对象上取出类型标识并进行判断，最后将不携带标识的原始数据对象应用于相应类型的程式即可。实例如下：

```scheme
(define (attach-tag type-tag contents)
  (cons type-tag contents))

(define (type-tag datum)
  (if (pair? datum)
      (car datum)
      (error "Bad tagged datum: TYPE-TAG" datum)))

(define (contents datum)
  (if (pair? datum)
      (cdr datum)
      (error "Bad tagged datum: CONTENTS" datum)))

(define (rectangular? z)
  (eq? (type-tag z) 'rectangular))

(define (polar z) (eq? (type-tag z) 'polar))

;; rectangular representation
(define (real-part-rectangular z) (car z))
(define (imag-part-rectangular z) (cdr z))
(define (magnitude-rectangular z)
  (sqrt (+ (square (real-part-rectangular z))
           (square (imag-part-rectangular z)))))
(define (angle-rectangular z)
  (atan (imag-part-rectangular z)
        (real-part-rectangular z)))
(define (make-from-real-imag-rectangular x y)
  (attach-tag 'rectangular (cons x y)))
(define (make-from-mag-ang-rectangular r a)
  (attach-tag 'rectangular
              (cons (* r (cos a)) (* r (sin a)))))

;; polar representation
(define (real-part-polar z)
  (* (magnitude-polar z) (cos (angle-polar z))))
(define (imag-part-polar z)
  (* (magnitude-polar z) (sin (angle-polar z))))
(define (magnitude-polar z) (sin (angle-polar z)))
(define (angle-polar z) (cdr z))
(define (make-from-real-imag-polar x y)
  (attach-tag 'polar
              (cons (sqrt (+ (square x) (square y)))
                    (atan y x))))
(define (make-from-mag-ang-polar r a)
  (attach-tag 'polar (cons r a)))
```

数据抽象是在垂直方向上建立[抽象屏障](obsidian://open?vault=SICP&file=methodology%26property%2FAbstraction%20Barriers)，帮助上一级屏障中的程式忽略下一级程式的具体实现，而类型标识能够帮助我们建立水平方向上的抽象屏障，能够在一个系统中兼容同一数据对象的不同表现方式。