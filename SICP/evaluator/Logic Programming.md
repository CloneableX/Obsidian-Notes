### 逻辑编程

查询系统通过在数据库中存储断言，并通过查询语句推演查询结果。而操作查询系统的编程语言就是逻辑编程，它能够向数据库中存储断言，使用基础查询，通过 `and`、`or`、`not` 和 `list-value` 组成复合查询。在复合查询的基础上通过规则，也就是 `rule` 进行抽象。

#### 断言
断言是数据库的物料，是事物的原子事实，当然你也可以认为它就是数据库中的数据。例如，存储的企业员工信息断言如下。

```scheme
(address (Bitdiddle Ben) (Slumerville (Ridge Road) 10))
(job (Bitdiddle Ben) (computer wizard))
(salary (Bitdiddle Ben) 60000)

(address (Hacker Alyssa P) (Cambridge (Mass Ave) 78))
(job (Hacker Alyssa P) (computer programmer))
(salary (Hacker Alyssa P) 40000)
(supervisor (Hacker Alyssa P) (Bitdiddle Ben))

(address (Fect Cy D) (Cambridge (Ames Street) 3))
(job (Fect Cy D) (computer programmer))
(salary (Fect Cy D) 35000)
(supervisor (Fect Cy D) (Bitdiddle Ben))

(address (Tweakit Lem E) (Boston (Bay State Road) 22))
(job (Tweakit Lem E) (computer technician))
(salary (Tweakit Lem E) 25000)
(supervisor (Tweakit Lem E) (Bitdiddle Ben))

(address (Reasoner Louis) (Slumerville (Pine Tree Road) 80))
(job (Reasoner Louis) (computer programmer trainee))
(salary (Reasoner Louis) 30000)
(supervisor (Reasoner Louis) (Hacker Alyssa P))
```

可以看到每个员工的信息被分拆成几个断言存储。

#### 查询

以上示例存储的断言可以通过基础查询从数据库中推演得到结果。例如，我们能够通过下列基础查询获取职位为 `(computer programmer)` 的员工。

```scheme
(job ?x (computer programmer))

;;; Query result:
(job (Hacker Alyssa P) (computer programmer))
(job (Fect Cy D) (computer programmer))
```

查询语句中出现了一个 `?x`，它是查询（模式）变量，变量存在命名的原因是在同一查询上下文中同名的模式变量代表了同样的数值。例如，我们可以通过下列查询获取所有职位为 `(computer programmer)` 的员工的住所。

```scheme
(and (job ?person (computer programmer))
	 (address ?person ?where))
```

除此之外，查询变量前还可以使用 `.` 表示多个元素匹配。例如，我们可以通过如下查询获取在 `computer` 部门下工作的员工。

```scheme
(job ?x (computer . ?type))
```

#### 规则

在查询的基础上我们可以通过规则抽象复合查询。例如，我们可以通过下列查询获取与 Bitdiddle Ben 居住在同一城市，并且职位为 `(computer programmer)` 的员工信息。

```scheme
(rule (lives-near ?person-1 ?person-2)
	  (and (address ?person-1 (?town . ?rest-1))
	       (address ?person-2 (?town . ?rest-2))
	       (not (same ?person-1 ?person-2))))

(and (job ?x (computer programmer))
	 (lives-near ?x (Bididdle Ben)))
```

可以发现规则的定义与程式的定义十分相似。

#### 查询系统如何运作

查询系统的驱动循环会读取终端输入的查询，也可能是断言或规则，如果是断言或规则需要通过特殊命令 `assert!` 声明，如下。

```scheme
(assert! (job (Bitdiddle Ben)
              (computer wizard)))
(assert! (rule (wheel ?person)
               (and (supervisor ?middle-manager ?person)
                    (supervisor ?x ?middle-manager))))
```

如果是断言或规则就根据索引存储在对应表中。如果是查询，会将查询与只有一个空框架的流传入 `qeval` 程式，由 `qeval` 甄别该查询属于哪种查询（特殊形式的复合查询或基础查询）进行相应的运算。

基础查询会根据查询的标识（当然该标识也可能是规则）查询对应索引表中的断言，并将断言的数据与模式变量匹配，匹配成功的就扩展初始框架（也就是空框架），匹配失败的需要记录失败标识。于是就产生一个扩展的框架流输出，然后根据扩展框架流对初始查询的拷贝实例化，也就是将变量值替换变量，产生最终查询输出的结果。

复合查询会按特殊形式的规则拆分至基础查询，然后按基础查询流程进行。需要注意的是 `not`，如果 `not` 查询中的变量没有预先被赋值会导致整个查询的结果为空，`lisp-value` 与之类似。

比较复杂的部分是规则部分，它需要将查询与规则一体化，也就是为变量赋值并使查询和规则相等，一体化成功后也会将绑定关系扩展至框架中。与基础查询有所区别的是一体化扩展的变量绑定的变量值也可能是一个变量，被绑定的变量可能会在后续的查询或规则一体化中绑定真正的数值，所以该扩展是有效的。

上述为查询系统的基础情况，如需要了解更多细节可以访问 [Logic Programming](https://github.com/CloneableX/SICP-learning/wiki/4.4-Logic-Programming)。