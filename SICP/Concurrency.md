### 并发

伴随着[赋值操作](obsidian://open?vault=SICP&file=procedures%2FSpecial%20Forms)的引入，时间属性也被同时引入了计算对象中。对于非时间性的[函数式编程](obsidian://open?vault=SICP&file=Imperative%20Programming)可以通过同时运行几组任务提高整个系统的计算效率，但是对于含有赋值操作的计算对象将涉及共享状态问题，在不加以限制的情况下进行并发操作只会导致错误的结果。

保持并发系统行为的正确性需要严格的条件，这些条件极度脆弱，所以不得不施加额外的限制以保障并发系统计算结果的正确性。关于并发系统的控制方法有很多，这里只介绍其中的一种 **串行器（serializer）**。

串行器能够将程式串行化，避免当前程式在执行过程中被其他进程的程式操作共享状态导致计算结果错误。具体使用方式如下。

```scheme
(define s (make-serializer))
(parallel-execute
  (s (lambda () (set! x (* x x))))
  (s (lambda () (set! x (+ x 1)))))
```

其中串行器的实现需要通过 **互斥元（mutex）** 机制实现，互斥元可以被持有和释放，但当它被持有时其他进程无法持有它，只能通过循环等待的方式等到互斥元被释放时才能持有。下面是通过互斥元实现的串行器。

```scheme
(define (make-serializer)
  (let ((mutex (make-mutex)))
    (lambda (p)
      (define (serialized-p . args)
        (mutex 'acquire)
        (let ((val (apply p args)))
          (mutex 'release)
          val))
      serialized-p)))
```

互斥元的实现通过 **单元（cell）** 中的值实现，单元是一个只有一个元素的列表，这个元素的值只能是 true 或 false，其中 true 表示互斥元处于持有状态，false 则相反。下列是关于互斥元的具体实现。

```scheme
(define (make-mutex)
  (let ((cell (list false)))
    (define (the-mutex m)
      (cond ((eq? m 'acquire)
             (if (test-and-set! cell)
                 (the-mutex 'acquire))) ;retry
            ((eq? m 'release) (clear! cell))))
    the-mutex))
(define (clear! cell) (set-car! cell false))
(define (test-and-set! cell)
  (if (car cell) true (begin (set-car! cell true) false)))
```

不过 `test-and-set!` 程式的实现并不满足标准，因为 `test-and-set!` 是并发系统的核心，整个操作要保持原子性，否则会由于并发操作导致计算结果错误，而 `test-and-set!` 的具体实现与系统对并发进程的运行处理有关。

除此之外，在面对多资源共享的情况下，还会出现由于不同进程互相持有对方正在等待的互斥元导致死锁问题。有些死锁问题可以通过添加控制机制避免，但有的死锁问题根本无法避免。