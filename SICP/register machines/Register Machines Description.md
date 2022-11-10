寄存器机器描述是一门用于描述[寄存器机器](obsidian://open?vault=SICP&file=register%20machines%2FRegister%20Machines)的语言，主要由指令构成。

### 指令

```scheme
(assign <register-name> (reg <register-name>))
(assign <register-name> (const <constant-value>))
(assign <register-name>
        (op <operation-name>)
        <input1> ... <inputn>)
(perform (op <operation-name>) <input1> ... <inputn>)
(test (op <operation-name>) <input1> ... <inputn>)
(branch (label <label-name>))
(goto (label <label-name>))
```

- `test`  为检测指令，能够执行指定检测
- `branch` 指令，也称条件分支指令，由控制器标识指定的路径，主要基于检测指令的检测结果进行选择。（检测指令和条件指令在控制器图示中以菱形组件的形式一同出现）。如果检测结果为 `false`，控制器将继续运行序列中的下一指令。否则，控制器应该继续运行 `branch` 标识之后的指令。
- `goto` 指令，也称非条件分支指令，根据标识名称跳转至需要继续执行的控制节点。
- `assign`  指令，能够将来自其他寄存器或常量的数据存储至指定寄存器中。
- `perform` 指令，能够执行 action，action 指某些操作产生的结果只会呈现于寄存器机器外部，例如 `print` 操作就是 action。

`goto` 指令除了可以直接跳转至指定标识节点，也可以通过存储于寄存器中的标识节点跳转，示例如下。

```scheme
(assign <register-name> (label <label-name>))
(goto (reg <register-name>))
```

除此之外，还有操作堆的指令，如下。

```scheme
(save <register-name>)
(restore <register-name>)
```

通过 `save` 指令能够将数据存入堆中，通过 `restore` 指令能够从堆中取出数据。在堆中存入一组数据后，通过 `restore` 会将这组数据按与存入顺序相反的顺序取出，也就是先入后出。

### 描述寄存器机器的语言

通过寄存器机器语言既能描述数据通路，也能描述控制器，但由于这样会导致阅读机器描述时要反复查阅操作定义和寄存器命名，所以我们选择将数据通路描述融入控制器描述的方式体现寄存器机器描述。忽略数据通路的部分主要是因为通过控制器理解寄存器机器的运转对我们更加重要，示例如下。

```scheme
(data-path
 (registers
  ((name a)
   (buttons ((name a<-b) (source (register b)))))
  ((name b)
   (buttons ((name b<-t) (source (register t)))))
  ((name t)
   (buttons ((name t<-r) (source (operation rem))))))
 (operations
   ((name rem) (inputs (register a) (register b)))
   ((name =) (inputs (register b) (constant 0)))))
(controller
 test-b
   (test =)
   (branch (label gcd-done))
   (t<-r)
   (a<-b)
   (b<-t)
   (goto (label test-b))
 gcd-done)
```

整合数据通路描述和控制器描述后如下。

```scheme
(controller
 test-b
   (test (op =) (reg b) (const 0))
   (branch (label gcd-done))
   (assign t (op rem) (reg a) (reg b))
   (assign a (reg b))
   (assign b (reg t))
   (goto (label test-b))
 gcd-done)
```

虽然这样也存在描述冗长、重复的问题，但基于我们关心的重点在后面的内容中都将使用这种机器描述。

### 迭代过程与递归过程

通过上述介绍的寄存器机器描述语言我们可以描述实现迭代过程和递归过程的寄存器机器。迭代过程比较简单，下列示例就是一个描述辗除法计算 GCD 值的寄存器机器。

```scheme
(controller
 test-b
   (test (op =) (reg b) (const 0))
   (branch (label gcd-done))
   (assign t (op rem) (reg a) (reg b))
   (assign a (reg b))
   (assign b (reg t))
   (goto (label test-b))
 gcd-done)
```

递归过程与迭代过程描述最大的不同在于，递归过程需要在子问题调用的计算结果完成后再进行主问题的下一步计算，所以在计算子问题时主问题的计算需要挂起，在子问题计算完成后恢复主问题的寄存器（因为子问题的计算过程可能修改寄存器的数据）数据继续主问题的运算。这时便需要堆这个数据结构的帮助，利用堆可以存储寄存器机器的数据，在需要时再从堆恢复数据。下列示例是一个计算阶乘的寄存器机器描述。

```scheme
(controller
   (assign continue (label fact-done))
 fact-loop
   (test (op =) (reg n) (const 1))
   (branch (label base-case))
   (save continue)
   (save n)
   (assign n (op -) (reg n) (const 1))
   (assign continue (label after-fact))
   (goto (label fact-loop))
 after-fact
   (restore n)
   (restore continue)
   (assign val (op *) (reg n) (reg val))
   (goto (reg continue))
 base-case
   (assign val (const 1))
   (goto (reg continue))
 fact-done)
```