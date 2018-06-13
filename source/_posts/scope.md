---
title: tf.name_scope() and tf.variable_scope()
date: 2018-03-05 19:46:57
tags: TensorFlow
categories: deep learning
---

TensorFLow有两个比较相似的scope: tf.name_scope和tf.variable_scope。
他们的区别在于name_scope对除tf.get_variable以外的创建op(包括变量op和运算op)有效，而variable_scope不仅对其他op有效，对tf.get_variable也有效。
```
with tf.name_scope('nscope'):
	a = tf.Variable(1, name='a', dtype=tf.float32)
	b = tf.get_variable(shape=[], name='b', dtype=tf.float32)
	c = a + b
```
以上代码创建了变量'nscope/a:0', 'b:0'以及运算'nscope/add'，name_scope对用tf.get_variable()创建的变量b无效。
```
with tf.variable_scope('vscope'):
	a = tf.Variable(1, name='a', dtype=tf.float32)
	b = tf.get_variable(shape=[], name='b', dtype=tf.float32)
	c = a + b
```
以上代码将创建变量'vscope/a:0', 'vscope/b:0'，以及运算'vscope/add'，variable_scope对它们都有效。
```
with tf.name_scope('nscope'):
	a = tf.Variable(1, name='a', dtype=tf.float32)
	with tf.variable_scope('vscope'):
		b = tf.get_variable(shape=[], name='b', dtype=tf.float32)
		c = a + b
```
以上代码为name_scope和variable_scope嵌套示例，将创建变量'nscope/a:0', 'nscope/vscope/b:0'和运算'nscope/vscope/add'。
利用name_scope和variable_scope的嵌套，可以方便地实现[高级API的多GPU使用](http://jzdss.club/2018/03/04/Multi-GPUs/)。为了真正理解name_scope和varialbe_scope，建议阅读。