---
title: Variable and ResourceVariable
date: 2017-09-15 11:15:15
tags: TensorFlow
categories: Deep Learning
---
关于ResourceVariable，tensorflow/python/ops/resource_variable_ops.py中的解释是:
<!-- more -->

>  Variable based on resource handles.
  See the ${variables} documentation for more details.
  A \`ResourceVariable\` allows you to maintain state across subsequent calls to session.run.
  The \`ResourceVariable\` constructor requires an initial value for the variable, which can be a \`Tensor\` of any type and shape. The initial value defines the type and shape of the variable. After construction, the type and shape of the variable are fixed. The value can be changed using one of the assign methods.
  Just like any \`Tensor\`, variables created with \`ResourceVariable()\` can be used as inputs for other Ops in the graph. Additionally, all the operators overloaded for the \`Tensor\` class are carried over to variables, so you can also add nodes to the graph by just doing arithmetic on variables.
  Unlike tf.Variable, a tf.ResourceVariable has well-defined semantics. Each usage of a ResourceVariable in a TensorFlow graph adds a read\_value operation to the graph. The Tensors returned by a read\_value operation are guaranteed to see all modifications to the value of the variable which happen in any operation on which the read\_value depends on (either directly, indirectly, or via a control dependency) and guaranteed to not see any modification to the value of the variable on which the read_value operation does not depend on.
  For example, if there is more than one assignment to a ResourceVariable in a single session.run call there is a well-defined value for each operation which uses the variable's value if the assignments and the read are connected by edges in the graph. Consider the following example, in which two writes can cause tf.Variable and tf.ResourceVariable to behave differently:
>```
    a = tf.ResourceVariable(1.0)
    a.initializer.run()
    assign = a.assign(2.0)
    with tf.control_dependencies([assign]):
      b = a.read_value()
    other_assign = a.assign(3.0)
    with tf.control_dependencies([other_assign]):
      tf.Print(b, [b]).run()  # Will print 2.0 because the value was read before
                              # other_assign ran.
  ```
>  To enforce these consistency properties tf.ResourceVariable might make more copies than an equivalent tf.Variable under the hood, so tf.Variable is still not deprecated.

可见官方认为和Variable相比，ResourceVariable有更明确的语义（well-defined semantics）。每次用到ResourceVariable变量都会在graph上添加一个read\_value节点，而每次用到Variable变量的值都是从同一个read节点引出的。这就造成某些情况下ResourceVariable和Variable的表现会不同。看下面两段代码：
1.
```
from tensorflow.python.ops import resource_variable_ops
sess = tf.InteractiveSession()
a = resource_variable_ops.ResourceVariable(1.0, name="a")
b = resource_variable_ops.ResourceVariable(2.0, name="b")

assign1 = a.assign(2.0)
assign2 = a.assign(3.0)

with tf.control_dependencies([assign1]):
    c = tf.add(a, b)

d = tf.multiply(a, c)

sess.run(tf.global_variables_initializer())
print(d.eval())

writer = tf.summary.FileWriter("./logs", sess.graph)
writer.flush()
writer.close()
```
2.
```
sess = tf.InteractiveSession()

a = tf.Variable(1.0, name="a")
b = tf.Variable(2.0, name="b")

assign1 = a.assign(2.0)
assign2 = a.assign(3.0)

with tf.control_dependencies([assign1]):
    c = tf.add(a, b)

d = tf.multiply(a, c)

sess.run(tf.global_variables_initializer())
print(d.eval())

writer = tf.summary.FileWriter("./logs", sess.graph)
writer.flush()
writer.close()
```
 第二段可以有稳定输出8，而第一段的结果可能为8也可能为4，原因不明。
  