---
title: Multi-GPUs
date: 2018-03-04 22:31:42
tags: TensorFlow
categories: deep learning
---
官网关于GPU的使用教程只有一篇:[Using GPUs](https://www.tensorflow.org/programmers_guide/using_gpu)。参考该教程仿照[cifai10示例代码](https://github.com/tensorflow/models/tree/master/tutorials/image/cifar10)用低级API(tf.nn)使用多GPU倒是足够，但官方对于高级API(tf.contrib等)的多GPU训练完全没有介绍。
<!-- more -->
多GPU训练时一般将变量定义在CPU上，把卷积等运算定义在各GPU上，如Fig.1所示。
<div align=center>
<img src="Multi-GPUs/flow chart.png" alt="Fig.1"/>
</div>
用高级API的多GPU训练时最大的问题也在于此。无论contrib还是slim，函数本身就包含了定义变量和运算。只要搞清如何将变量和运算分别定义在CPU和GPU上，后面也就和cifar10示例一样了。

我的思路是先在CPU上构建一个完整的图，包括变量和运算，但后续不会用到这里创建的运算操作。也就是说这一步在CPU上定义了所有变量。
```
def net(x):
	res = tf.contrib.layers.conv2d(x, 10, [3, 3])
	...
	return res

with tf.device('/CPU:0'):
	y = net(x)
```

这些高级API都是用tf.get_variable()创建的变量，因此我们可以利用tf.name_scope()和tf.variable_scope()的特性来实现目的。tf.name_scope()和tf.variable_scope()的区别参考[这里](http://jzdss.club/2018/03/05/scope/)。

```
with tf.device('/CPU:0'):
	with tf.name_scope('CPU'):
		net(x)

for i in range(num_gpus):
	with tf.device('/GPU:%d' % i):
		with tf.name_scope('GPU_%d' % i):
			with tf.variable_scope(tf.get_variable_scope(), reuse=True):
				y = net(x)
				loss = ...
				grad = ...
```
loss,grad等计算仿照cifar10示例，就可以实现高级API的多GPU训练了。