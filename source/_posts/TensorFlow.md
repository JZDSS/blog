---
title: TensorFlow
date: 2017-09-13 21:31:30
tags: TensorFlow
categories: deep learning
---
### TensorBoard
利用TensorBoard查看graph有助于理解TensorFlow。先了解一下如何保存log。下面的代码定义了两个常量和一个加法操作并保存到logs目录下。
<!-- more -->
```
a = tf.constant(1, name="lala")
b = tf.constant(2, name="lala2")
c = a + b
with tf.Session() as sess:
	writer = tf.summary.FileWriter("./logs", sess.graph)
	writer.flush();
	writer.close()
```
终端执行以下命令后用浏览器打开[localhost:6006](localhost:6006)即可利用TensorBoard查看graph，如Fig.1。图中的常量名是定义常量时name参数的值，和python中的变量名无关。
```
tensorboard --logdir ./logs
```
<div align=center>
<img src="TensorFlow/fig1.png" alt="Fig.1"/>
</div>
当然我们也可以自定义端口号，详情请执行tensorflow --help查看帮助。
后面的代码都可以用这种方式来实现graph的可视化。




函数内定义的变量/常量也是在graph中添加节点，不会因为函数结束而消失。当graph中已存在某个名字的节点时会自动编号，如下面代码中的第二个ADD\_OP被命名成了ADD\_OP\_1，如Fig.2。
```
def foo(input, name):
	a = tf.constant(1, name=name)
	return tf.add(input, a, name="ADD_OP")


a = tf.constant(2, name="lala")
a = foo(a, "aa")
foo(a, "bb")

with tf.Session() as sess:
	writer = tf.summary.FileWriter("./logs", sess.graph)
	writer.flush();
	writer.close()
```
<div align=center>
<img src="TensorFlow/fig2.png" alt="Fig.2"/>
</div>
既然函数中定义的变量仍然存在graph中，而函数foo中的a的作用域仅仅为函数内，这就有了在函数外如何调用“aa”和“bb”的问题。

### 变量复用
TensorFlow中不存在常量复用，所以想要在函数外操作“aa"和”bb“我们只能将它们作为函数的返回值来实现在函数外对”aa“和”bb“的操作。但我们可以复用变量。
TensorFlow中有两种创建变量的方式：
1. tf.Variable()
2. tf.get_variable()

官方推荐使用第二种方式创建变量，因为第二种方式支持变量复用。详见[官网教程](https://www.tensorflow.org/programmers_guide/variables)。

本来想写一个好点的入门教程的，但后来看到Stanford已经开了TensorFlow的课程了，就不继续写了。链接为：[CS 20SI: Tensorflow for Deep Learning Research](http://web.stanford.edu/class/cs20si/index.html) 。
