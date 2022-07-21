---
title: 【Introduction to TensorFlow】02 初识机器学习与计算机视觉
date: 2019-10-29 19:41:53
tags:
- 世界之内
- 机器学习
---

本节内容包括课程week1和week2的内容，week1主要介绍了机器学习的思维逻辑，并以TF中的hello world作为示例，加深理解机器学习的范式。week2将范式的概念运用在真实的应用场景上，基于数据集Fashion MNIST实现了对10种衣物的识别任务，新的操作有：数据加载处理、模型评估、callback。

<!--more-->

# A New Programming Paradigm

## 主要内容

- 两种不同的范式paradigm
- hello world demo

## 独特的范式

程序编码的范式：

![programmingParadigm](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/tf-02-1.png)

机器学习的范式：

![machineLearningParadign](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/tf-02-2.png)

两者有截然不同的范式，机器学习的有趣之处就是我们能让计算机自己学习rules。机器学习就是计算机学习 「区分事物的模式」。

## Hello World

学习了理论，开始coding来加深机器学习范式的理解。

现在我知道x=[-1,0,1,2,3,4]时对应的y=[-3,-1,1,3,5,7]，那么当x=10的时候，对应的y等于多少？将这个问题抽象化：已知data（x）和answers（y），求y->x，即一个映射。这是机器学习的范式。而求解的方法，就可以用神经网络来实现，神经网络只是稍微高级一点的机器学习方法，现在被叫做深度学习。神经网络就是一组能够学习模式的函数集合而已。

代码如下：

```python
model = keras.Sequence([keras.layers.Dense(units=1, input_shape=[1])])
# Sequence，神经网络层在这里定义，好几层网络连在一起，所以才称之为sequence。
# dense，定义了1层相连的神经元，这里只有1个dense，即1层。units表示这一层中有几个神经元，这里只有1个。所以整个结构只有1层神经元，这一层里只1个神经元。然后，input_shape定义了input的形状，这里只有一个值。

model.compile(optimizer='sgd', loss='mean_squared_error')
# Compile，内部参数是损失函数和优化器。
# 损失函数：神经网络不知道x和y的关系，所以瞎猜。损失函数来衡量它猜测的好坏。
# 优化器：损失函数把好坏的结果给优化器，然后优化器使得神经网络下一次瞎猜的结果变得更好。这样一轮轮地瞎猜-评价-改正下去，最终正确率会变成接近100%，这就叫收敛（Converge）。

xs = np.array([-1.0, 0.0, 1.0, 2.0, 3.0, 4.0], dtype=float)
ys = np.array([-3.0, -1.0, 1.0, 3.0, 5.0, 7.0], dtype=float)

model.fit(xs, ys, epochs=10)
# fit，开始模型的训练，epoch表示训练几次，也就是上面所说的，瞎猜-评价-改正多少次。

print(model.predict([10.0]))
# predict，模型已经训练好，rule已经解出来了，所以接下来的预测就是一个rule+data、得到output的过程。
```

实际上，当我们输入10，结果不是19，是一个和19很接近的数。比如18.72。为什么？

- 我们只给了6个点，虽然目前来看是线性的，但是实际上，不能确保之后的点都是线性的。
- 神经网络不确定之后的解是否也是线性的，只能说x=10时，y=19的可能性很大，但是不一定。



# Introduction to Computer Vision

## 主要内容

- Fashion MNIST介绍
- 训练神经网络，使得计算机可以识别Fashion MNIS中的10中衣物。

上一节学习了机器学习的范式，并学会用TF通过数据和答案得到两者之间的rules。本节课的目的是将这种行为运用在实际应用中——计算机视觉。上一节我们说到，机器学习就是计算机学习 「区分事物的模式」，本节我们就想要用数据+答案的方式，使得计算机能够学习到区分某一个事物的模式。

具体来说，我们有一个数据集Fashion MNIST：

- 70k images
- 10 categories
- Images are 28X28 in gray scale *
- Can train a neural net

数据集中有10种不同的物品，衣服、鞋子、裤子……我们的目的就是，数据（images）+答案（是衣服还是鞋子还是……），来让计算机学习到区分这10样东西的模式。

## 加载数据集

```python
fashion_mnist = keras.datasets.fashion_mnist
(train_images,train_labels),(test_images,test_labels)=fashion_minist.load_data()
```

load_data，返回四个列表

60k用来训练，10k测试

为什么用数字作为标识，而不是直接用文字比如 ”ankle boot“？

- 计算机适合处理数字
- 帮助我们减少偏见

## Normalizing

```python
training_images  = training_images / 255.0
test_images = test_images / 255.0
```

normalizing，使得每个pixel保持在0-1之间，有利于训练。如果仍在0-255，那么造成的loss会很大。

## 定义神经网络

```python
model = keras.Sequence([
    keras.layers.Flatten(input_shape(28,28)),
  # flatten，讲一个28*28的二维矩阵编程一维的简单线性数组
    keras.layers.Dense(128,activation=tf.nn.relu), 
  #middle layer/ hidden layer#
    keras.layers.Dense(10,activation=tf.nn.softmax)
  # output layer，有十个物品需要识别，对应就是10个神经元
])
```

middle/ hidden layer，实现的其实是一个映射，如下图：

![cv-nueral](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/tf-02-3.png)

就像我们上一节的内容，我们还是可以把它简单理解成一个求函数的过程。这里有128个神经元，每一个神经元相当于一个系数，可以称呼它们为$w_1,w_2,w_3,...,w_{128}$

这次求的不是y=2x-1，而是更复杂的，使得当x=一张靴子的图片时，y=9。其实神经网络做的本质上就是一种映射。

## 训练评估

```python
model.compile(optimizer = tf.train.AdamOptimizer(),
              loss = 'sparse_categorical_crossentropy',
              metrics=['accuracy'])

model.fit(training_images, training_labels, epochs=9)
# epoch如果太多，会造成过拟合。

model.evaluate(test_images, test_labels)
```

## Callback

准确率不需要太高，如果太高不一定是一件好事，也许是overfiting。因此，可以使用callback，当模型准确率达到我们要求的值时，就停止训练。

首先定义一个callback类

```python
class myCallback(tf.keras.callbacks.Callback):
  def on_epoch_end(self, epoch, logs={}):
    if(logs.get('loss')<0.4): # 当准确率到0.6时停止
      print("\nReached 60% accuracy so cancelling training!")
      self.model.stop_training = True
```

作为变量放入在model.fit中

```python
callbacks = myCallback()

model = keras.Sequence(...)
model.compile(...)
model.fit(...)
model.fit(training_images, training_labels, epochs=9, callbacks=[callbacks])
```









