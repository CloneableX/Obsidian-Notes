在 Lisp 中列表结构是一种常用数据结构，它的基础是序对，同时序对也是很多数据对象的基础结构，为了揭示列表结构的数据操作在计算机中的细节，我们需要研究在现代计算机存储器中如何实现列表结构及其操作。

### 将存储看作向量

常规计算机的存储器通常可以视作一组连续的存储单元，而且每个存储单元都有一个独一无二的 **地址（address）**。一组连续的存储信息可以通过基准存储地址与偏移量的加法运算得出，所以这种存储方式并不会由于连续存储信息的增长而增加获取指定地址内存储信息的消耗时间。根据这种概念，我们可以将计算机存储器映射为数据结构 **向量（vector）**，向量中的每个元素都可以通过索引访问，于是通过下列 Scheme 基础程式便可以操作向量。

```scheme
(vector-ref <vector> <n>)         ; 将返回向量的第 n 个元素
(vector-set! <vector <n> <value>) ; 将向量的第 n 个元素设置为指定值
```

### 各种数据在向量概念上的实现

我们可以将计算机存储器看作两个独立的向量 `the-cars` 和 `the-cdrs` 。分别存储 `car` 和 `cdr` 的数据，而 `car` 和 `cdr` 中保存的只是对应的向量索引的指针。除了序对之外，Lisp 还需要存储其他类型的数据。为了区分不同类型的指针会附带类型信息，这种指针也称为 **类型指针（typed pointer）**。例如，数字 4 的指针为 `n4`，其中 `n` 表示该指针类型是数字；存储在向量索引为 4 的序对指针为 `p4`，其中 `p` 表示该指针类型为序对。

除了上述情况外标识数据会比较特别，Lisp 在生成标识前会先读取标识字符序列，然后在 **对象表（obarray）** 中查找该标识是否已经存在（也就是已经存在对应的指针），如果已经存在则返回该指针，如果不存在则需要生成新的标识并保存在对象表中。

#### 列表基础操作

现在我们可以将列表基础操作转换为寄存器机器指令，具体实现如下。

```scheme
(assign <reg1> (op vector-ref) (reg the-cars) (reg <reg2>)) ; implementation of (assign <reg1> (op car) (reg <reg2>))
(assign <reg1> (op vector-ref) (reg the-cdrs) (reg <reg2>)) ; implementation of (assign <reg1> (op cdr) (reg <reg2>))

(perform
 (op vector-set!) (reg the-cars) (reg <reg1>) (reg <reg2>)) ; implemetation of (perform (op set-car!) (reg <reg1>) (reg <reg 2>))
(perform
 (op vector-set!) (reg the-cdrs) (reg <reg1>) (reg <reg2>)) ; implementation of (perform (op set-cdr!) (reg <reg1>) (reg <reg2>))

;;; implementation of (assign <reg1> (op cons) (reg <reg2>) (reg <reg3>))
(perform
 (op vector-set!) (reg the-cars) (reg free) (reg <reg2>))
(perform
 (op vector-set!) (reg the-cdrs) (reg free) (reg <reg3>))
(assign <reg1> (reg free))
(assign free (op +) (reg free) (const 1))
```

其中 `free` 寄存器持有指向下一可用存储单元的索引。

#### 堆栈基础操作

因为堆栈也是在列表的基础上实现的，所以它的相关指令也可以通过向量指令实现。

```scheme
(assign the-stack (op cons) (reg <reg>) (reg the-stack))) ; implementation of (save <reg>)

;;; implementation of (restore <reg>)
(assign <reg> (op car) (reg the-stack))
(assign the-stack (op cdr) (reg the-stack))
```

