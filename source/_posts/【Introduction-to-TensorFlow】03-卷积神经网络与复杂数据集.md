---
title: 【Introduction to TensorFlow】03 卷积神经网络与复杂数据集
date: 2019-10-31 23:30:00
tags:
- 机器学习
- 世界之内
---

上一节使用了几层简单的神经网络实现了对Fashion MNIST数据集中10种衣物的识别。我们将一张图的每一个像素都作为一个参考因素，来训练我们的模型，但是其实一张图有很多没有用的地方，这就给训练模型增加了负担。那么接下来要学习的，就是一种能够少注意图像冗余信息，多注意图像重要信息的模型。本节包括week3和week4的内容。

<!--more-->

week3讲述了CNN，卷积神经网络，一种压缩图像、获取图像特征的模型。本节首先概述CNN的重要概念：卷积，池化，然后使用TF实现这两个概念，搭建一个卷积神经网络，在Fashion MNIST中训练。

Fashion MNIST的图片是28*28的小图，且所有的物体都居中展示。week4则将进一步学习如何使用TF的API对更大、更复杂、更不整齐的图像进行预处理，并且在预处理后的图像上施展CNN的魔法。

---------------------

# Enhancing Vision with Convolutional Neural Networks

## 主要内容

- 卷积与池化
- 使用卷积神经网络，提高上一节中仅使用两层神经网络识别Fashion MNIST 中10种衣物的准确率

## Convolution

对于每一个像素，我们要求不仅关注这个像素，还要关注这个像素周围的像素。如果我们有一个filter 大小为3*3，那么我们就会关注这个像素上下左右一圈的8个像素，我们关注它们的方法就是将每个像素和filter中对应的值相乘，最后再一起相加。

上例子：

![tf-02-1](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/tf-02-1.png)

filter就这样一直滑动，每一次滑动得到一个值，每个值拼在一起，形成一张新的图像。filter还有滑动步长，stride，一次走一步，还是两步，得到的结果也会不一样。

filter滑动后的新图像大小有一个公式：

$$OutputSize=(N-F)/stride+1$$

where N 是原始图像宽度，F是filter的大小。这里默认padding=0。

卷积是一种神奇的方法，就是通过filter的过滤，能够使得图片的某些特征得到强调，filter的值不同，提取的特征就会不同。

如下图，就通过一个简单的filter得到了事物的轮廓。

![tf-02-2](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/tf-02-2.png)

这就是卷积，一种提取图片特征的方法。

> 这种做法的两个关键启发：
>
> 1. Features are **hierarchical**。从低复杂度的特征中组合出高复杂度的特征，比直接学习高复杂度的特征更加有效。比如我可以先学习识别直线、圆形之类的分类器，再训练一个组合圆形、直线这些基本图形的分类器
> 2. Features are translationally invariant。可以保证特征的平移不变性（一定程度），可以提高模型的泛化效果。

from [【CS20-TF4DL】06 Convnet 简介](http://wdxmzy.com/course/cs20-06/2019/08/13/)

## Pooling

池化的目的是为了缩小图像，将一些冗余的信息直截了当的去掉。

这里只讲Max Pooling，简单来说就是在一定区域中，比如2 * 2的像素中，选择像素值最大的像素，其他全部抛弃。

![tf-02-3](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/tf-02-3.jpg)

也可以把max pooling想象成有一个filter，在图像上滑动，滑动的步伐大小叫stride。

一般来说，stride会和filter一样大小，上图中，filter=2 * 2，stride=2。图像小了一半。

我们接着看例子，下图是一个经过卷积的图片：

![tf-02-4](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/tf-02-4.png)

我们使用filter大小为2*2的max pooling，使图片变成了原来的一半，非常神奇的是，缩小了一半的图片不仅没有失去原来的特征，反而更加明显了，这说明池化使得图片的冗余信息减少了。

![tf-02-5](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/tf-02-5.png)

从上面这个例子看，pooling能够有效地减少冗余信息。

## Convolution & Pooling in TF

讲完了理论，接下来看代码。我们在上一节的全连接神经网络上增加了卷积和池化层：

```python
model = tf.keras.models.Sequential([
	tf.keras.layers.Conv2D(64, (3,3), activation='relu', input_shape=(28,28,1)),
  # 第一个参数是filter的个数，这里有64个filter。第二个参数是filter的大小，这里是3*3，stride默认为1.input_shape大小是28*28*1，这里1的意思是只有一个颜色通道，就是灰度图，如果是rgb图，就有三种颜色通道，就是28*28*3.
	tf.keras.layers.MaxPooling2D(2, 2),
  # max pooling，filter的大小是2*2，stride默认为1.
	tf.keras.layers.Conv2D(64, (3,3), activation='relu', input_shape=(28,28,1)),
  # 同上
	tf.keras.layers.MaxPooling2D(2, 2),
  # 同上
	tf.keras.layers.Flatten(),
  # 然后再把图像展开编程一维数组，不用填参数，TF会自动算出图像大小
	tf.keras.layers.Dense(128, activation='relu'),
	tf.keras.layers.Dense(10, activation='softmax')
])
```

### model.summary

`model.summary`能够显示模型的网络结构，下图是我们的网络结构：

![tf-02-6](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/tf-02-6.png)

一层层看，第一层卷积层，输出的shape由原来的(28, 28, 1)变成了(26，26，64)，第二层pooling后，因为filter为2\*2，所以之间变为原来的一半(13, 13, 64)，再经过一层卷积核池化，到最后输出为(5, 5, 64)。

一张28\*28的图片，在经过卷积池化后，变成了64张5\*5的图片，我们可以直观地理解为，提取了64个这张图片的特征，每个特征大小为5*5。

# Using Real-world Images

## 主要内容

- 处理复杂、不统一的数据集：image generator
- 使用处理后的数据集训练：fit generator

## Image Generator 

我们之前使用的都是非常完美的数据集，图片大小相同、物体居中且清晰，但是当我们有一个数据集，里面的图片大小不同，且物体的位置并不居中，图中有不止一个物体，而且更糟糕的是，我们还没做label，该怎么办？别怕，用TF的API：image generator。

只要我们把同一类的图片放在同一个文件夹下，我们就能用这个API来自动打标签，标签就是这个文件夹的名字。

如图，我的图片都没打标签，但是都放在了文件夹里，那么就可以放心地用image generator了。

![tf-02-7](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/tf-02-7.png)

实现的代码如下：

```python
from tensorflow.keras.preprocessing.image import ImageDataGenerator

train_datagen = ImageDataGenerator(rescale=1.0/255.0)

train_generator = train_datagen.flow_from_directory(
	train_dir,
  # 这个表示训练集的文件夹名称
  target_size = (300, 300),
  # 图片大小不一，但是训练神经网络，数据大小一定要相同，target_size就设定了我们想要的大小，使得每一个图片都变成这个大小
  batch_size = 128,
  # 我们不是一张张训练，而是一个batch训练，这样的效果会比一张张训练更好
  class_mode = 'binary'
  # 我们这里是一个二分类问题，这个参数是为了给图片打标签
)
```



## CNN on Complex Images

### Model

对于更复杂的数据集，我们定义了一个更大的卷积神经网络，代码如下：

```python
model = tf.keras.models.Sequential([
    # Note the input shape is the desired size of the image 300x300 with 3 bytes color
    # This is the first convolution
    tf.keras.layers.Conv2D(64, (3,3), activation='relu'),
    tf.keras.layers.MaxPooling2D(2,2),
    tf.keras.layers.Conv2D(64, (3,3), activation='relu'),
    tf.keras.layers.MaxPooling2D(2,2),
    tf.keras.layers.Conv2D(64, (3,3), activation='relu'),
    tf.keras.layers.MaxPooling2D(2,2),
    # Flatten the results to feed into a DNN
    tf.keras.layers.Flatten(),
    # 512 neuron hidden layer
    tf.keras.layers.Dense(512, activation='relu'),
    # Only 1 output neuron. It will contain a value from 0-1 where 0 for 1 class ('horses') and 1 for the other ('humans')
    tf.keras.layers.Dense(1, activation='sigmoid')
])
```

和上一节差不多，多了两组卷积池化层，就不解释了。

### Compile

上一节compile的optimizer是adam，loss是“sparse_categorical_crossentropy”，这次optimizer换成“RMSprop”，loss换成“binary_crossentropy”。RMSprop使得梯度下降变得更加稳定，且能容忍更大的学习率。

代码如下：

```python
from tensorflow.keras.optimizers import RMSprop

model.compile(loss='binary_crossentropy',
             optimizer=RMSprop(lr=0.001),
             metrics=['acc'])
```

### Training

还记得之前的fit吗？

```python
model.fit(x_train, y_train, epochs=10)
```

因为这次我们的x_train不是直接从数据集里拿的，而是使用ImageDataGenerator这个API对数据进行预处理，所以相应的fit也要改。

名字和ImageDataGenerator 很般配：fit_generator。是这样用的：

```python
history = model.fit_generator(
			train_generator,
  # 这就是之前我们写过的
			steps_per_epoch = 8,
  # 每个epoch训练几次batch。我们在train_generator中定义了一个batch大小是128，我们一共有1024张图片，所以这里设置为8
			epochs = 10,
			verbose = 2
  # verbose：日志显示
  # verbose = 0 为不在标准输出流输出日志信息
  # verbose = 1 为输出进度条记录
  # verbose = 2 为每个epoch输出一行记录
  # verbose默认为 1
)
```

### Validation

我们可以在训练的时候，增加一组验证集。

验证集的预处理和训练集的预处理一样：

```python
from tensorflow.keras.preprocessing.image import ImageDataGenerator

validate_datagen = ImageDataGenerator(rescale=1.0/255.0)

validate_generator = validate_datagen.flow_from_directory(
		validate_dir,
		target_size = (300, 300),
		batch_size = 32,
		class_mode = 'binary'
	)
```

在原来的fit_generator中加入validate_generator即可：

```python
history = model.fit_generator(
			train_generator,
			steps_per_epoch=8,
			epochs=10,
			validation_data=validate_generator,
	# 我们加在了这里
			validation_steps=8,
  # 同steps_per_epoch
			verbose=2)
```

