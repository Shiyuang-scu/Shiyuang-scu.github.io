---
title: Softmax Regression
date: 2018-09-08 18:03:47
tags: 世界之内
---
![Softmax-0](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/Softmax-0.jpg)
# Abstract
*Softmax Regression是Logistic Regression的推广*

*LR能做的是区分两类，而SR则能区分n类，虽然两者的本质都是线性的*

<!--more-->
# Logistic Regression的回顾

对于数据集{(x^1,y^1) , ... , (x^m,y^m)},y只有两个类别，我们记为{0,1}，LR的假设函数(hypothesis function) 如下：

![Softmax-1](http://deeplearning.stanford.edu/wiki/images/math/b/b/3/bb3791d463b832a88731b94f1d8e5279.png)



我们的目的是训练参数theta，使得可以拟合原数据集，则代价函数为：

![Softmax-2](http://deeplearning.stanford.edu/wiki/images/math/f/a/6/fa6565f1e7b91831e306ec404ccc1156.png)

# 假设函数

对于给定的测试输入 ![\textstyle x](http://deeplearning.stanford.edu/wiki/images/math/f/6/c/f6c0f8758a1eb9c99c0bbe309ff2c5a5.png)，我们想用假设函数针对每一个类别j 估算出概率值 ![\textstyle p(y=j | x)](http://deeplearning.stanford.edu/wiki/images/math/c/1/d/c1d5aaee0724f2183116cb8860f1b9e4.png)。也就是说，我们想估计 ![\textstyle x](http://deeplearning.stanford.edu/wiki/images/math/f/6/c/f6c0f8758a1eb9c99c0bbe309ff2c5a5.png) 的每一种分类结果出现的概率。因此，我们的假设函数将要输出一个 ![\textstyle k](http://deeplearning.stanford.edu/wiki/images/math/b/0/0/b0066e761791cae480158b649e5f5a69.png) 维的向量（向量元素的和为1）来表示这 ![\textstyle k](http://deeplearning.stanford.edu/wiki/images/math/b/0/0/b0066e761791cae480158b649e5f5a69.png) 个估计的概率值。 具体地说，我们的假设函数 ![\textstyle h_{\theta}(x)](http://deeplearning.stanford.edu/wiki/images/math/8/8/7/887e72d0a7b7eb5083120e23a909a554.png) 形式如下：

![Softmax-3](http://deeplearning.stanford.edu/wiki/images/math/a/1/b/a1b0d7b40fe624cd8a24354792223a9d.png)

其中 ![\theta_1, \theta_2, \ldots, \theta_k \in \Re^{n+1}](http://deeplearning.stanford.edu/wiki/images/math/f/d/9/fd93be6ab8e2b869691579202d7b4417.png) 是模型的参数。请注意 ![\frac{1}{ \sum_{j=1}^{k}{e^{ \theta_j^T x^{(i)} }} } ](http://deeplearning.stanford.edu/wiki/images/math/a/a/b/aab84964dbe1a2f77c9c91327ea0d6d6.png)这一项对概率分布实际上进行归一化，使得所有概率之和为 1，这也是Softmax分类器的精髓。

# 代价函数

回望Logistic Regression，我们的代价函数中，有(1-y^i)和(y^i)原因就在于，y只能是0或者1，所以在计算代价的时候，能够用这种形式来去除不属于y=0或者1的结果。但是在Softmax中，y不只是等于0、1了，所以我们这样定义：

在下面的公式中，![\textstyle 1\{\cdot\}](http://deeplearning.stanford.edu/wiki/images/math/b/2/7/b279688f53460dc80e6a81235beee14d.png) 是示性函数，其取值规则为：

![\textstyle 1\{](http://deeplearning.stanford.edu/wiki/images/math/2/5/4/25481caeef48c5fa12aa22988d931716.png) 值为真的表达式}=1， ![\textstyle 1\{](http://deeplearning.stanford.edu/wiki/images/math/2/5/4/25481caeef48c5fa12aa22988d931716.png) 值为假的表达式 ![\textstyle \}=0](http://deeplearning.stanford.edu/wiki/images/math/1/1/2/11274c6dbf36bae0b02acd07555f4ce7.png)。举例来说，表达式 ![\textstyle 1\{2+2=4\}](http://deeplearning.stanford.edu/wiki/images/math/6/8/1/68118d4cdfdbe134b420cc4031f3c46e.png) 的值为1 ，![\textstyle 1\{1+1=5\}](http://deeplearning.stanford.edu/wiki/images/math/4/5/f/45f90c8f2e9a8d2f00dff993e45c9dbd.png)的值为 0。我们的代价函数为：

![Softmax-4](http://deeplearning.stanford.edu/wiki/images/math/7/6/3/7634eb3b08dc003aa4591a95824d4fbd.png)


值得注意的是，上述公式是logistic回归代价函数的推广。logistic回归代价函数可以改为：

![Softmax-5](http://deeplearning.stanford.edu/wiki/images/math/5/4/9/5491271f19161f8ea6a6b2a82c83fc3a.png)


可以看到，Softmax代价函数与logistic 代价函数在形式上非常类似，只是在Softmax损失函数中对类标记的 ![\textstyle k](http://deeplearning.stanford.edu/wiki/images/math/b/0/0/b0066e761791cae480158b649e5f5a69.png) 个可能值进行了累加。注意在Softmax回归中将 ![\textstyle x](http://deeplearning.stanford.edu/wiki/images/math/f/6/c/f6c0f8758a1eb9c99c0bbe309ff2c5a5.png) 分类为类别 ![\textstyle j](http://deeplearning.stanford.edu/wiki/images/math/2/3/5/235c5146ab110558897640c34dad7d97.png) 的概率为：

![Softmax-6](http://deeplearning.stanford.edu/wiki/images/math/a/2/e/a2e69ec139cdd4828130c175d990d4e3.png)


对于 ![\textstyle J(\theta)](http://deeplearning.stanford.edu/wiki/images/math/c/e/0/ce027336c1cb3c0cd461406c81369ebf.png) 的最小化问题，目前还没有闭式解法。因此，我们使用迭代的优化算法（例如梯度下降法，或 L-BFGS），步骤和在LR中的一样。



# 参数冗余

Softmax有一个很神奇的特性，它存在参数冗余。什么意思呢？举个例子：

假设我们从参数向量 ![\textstyle \theta_j](http://deeplearning.stanford.edu/wiki/images/math/3/7/e/37e2eaf89c7b1f26381f438a0367099a.png) 中减去了向量 ![\textstyle \psi](http://deeplearning.stanford.edu/wiki/images/math/2/0/0/200d05b77b27ed6a0aa466165f660b64.png)，这时，每一个 ![\textstyle \theta_j](http://deeplearning.stanford.edu/wiki/images/math/3/7/e/37e2eaf89c7b1f26381f438a0367099a.png) 都变成了 ![\textstyle \theta_j - \psi](http://deeplearning.stanford.edu/wiki/images/math/9/b/8/9b8020b23b66a91888062b4a9d8902c5.png)(![\textstyle j=1, \ldots, k](http://deeplearning.stanford.edu/wiki/images/math/8/3/a/83aaa94ba392e98b15d29ed67fdaae12.png))。此时假设函数变成了以下的式子：

![Softmax-7](http://deeplearning.stanford.edu/wiki/images/math/d/8/0/d8076908fb40b49db821dc410b03700f.png)

换句话说，从 ![\textstyle \theta_j](http://deeplearning.stanford.edu/wiki/images/math/3/7/e/37e2eaf89c7b1f26381f438a0367099a.png) 中减去 ![\textstyle \psi](http://deeplearning.stanford.edu/wiki/images/math/2/0/0/200d05b77b27ed6a0aa466165f660b64.png) 完全不影响假设函数的预测结果！这表明前面的 softmax 回归模型中存在冗余的参数。更正式一点来说， Softmax 模型被过度参数化了。对于任意一个用于拟合数据的假设函数，可以求出多组参数值，这些参数得到的是完全相同的假设函数 ![\textstyle h_\theta](http://deeplearning.stanford.edu/wiki/images/math/3/2/b/32b8c2324fe254830c693b4e9a62cad6.png)。具体来说，如果参数 ![\textstyle (\theta_1, \theta_2,\ldots, \theta_k)](http://deeplearning.stanford.edu/wiki/images/math/6/b/8/6b8d6eff611b23105ff5876c348e19b2.png) 是代价函数 ![\textstyle J(\theta)](http://deeplearning.stanford.edu/wiki/images/math/c/e/0/ce027336c1cb3c0cd461406c81369ebf.png) 的极小值点，那么 ![\textstyle (\theta_1 - \psi, \theta_2 - \psi,\ldots, \theta_k - \psi)](http://deeplearning.stanford.edu/wiki/images/math/e/d/3/ed3ccb28d5145b361d3396fca429a751.png) 同样也是它的极小值点，其中 ![\textstyle \psi](http://deeplearning.stanford.edu/wiki/images/math/2/0/0/200d05b77b27ed6a0aa466165f660b64.png) 可以为任意向量。因此使 ![\textstyle J(\theta)](http://deeplearning.stanford.edu/wiki/images/math/c/e/0/ce027336c1cb3c0cd461406c81369ebf.png) 最小化的解不是唯一的。

那我们可以直接把 ![\textstyle \psi = \theta_1](http://deeplearning.stanford.edu/wiki/images/math/5/0/6/5068a95b00ca4021de31c92d1b9265eb.png) 嘛，这样一来，原本是 ![\textstyle \theta_1](http://deeplearning.stanford.edu/wiki/images/math/f/5/3/f538938bbd1f7000bcc2c9d990d53632.png)的地方就变成了0，式子简化了不少。

接下来我们就利用参数冗余这个特点，来推导**SR其实就是LR的普遍形式，LR是SR的分类数n=2时的特殊情况**这个结论。

当 ![\textstyle k = 2](http://deeplearning.stanford.edu/wiki/images/math/4/0/9/409483805c4d8c79f734a131d859b9f7.png) 时，softmax 回归的假设函数为：

![Softmax-8](http://deeplearning.stanford.edu/wiki/images/math/e/3/2/e32efab7bff7353e04775b030af0dae9.png)


利用softmax回归参数冗余的特点，我们令 ![\textstyle \psi = \theta_1](http://deeplearning.stanford.edu/wiki/images/math/5/0/6/5068a95b00ca4021de31c92d1b9265eb.png)，并且从两个参数向量中都减去向量 ![\textstyle \theta_1](http://deeplearning.stanford.edu/wiki/images/math/f/5/3/f538938bbd1f7000bcc2c9d990d53632.png)，得到:

![Softmax-9](http://deeplearning.stanford.edu/wiki/images/math/b/8/1/b81d6e553283fadddbe29fe55226fb38.png)

因此，用 ![\textstyle \theta'](http://deeplearning.stanford.edu/wiki/images/math/4/1/8/418c4d9ed2c50151474385a534eb3537.png)来表示![\textstyle \theta_2-\theta_1](http://deeplearning.stanford.edu/wiki/images/math/f/1/d/f1dd3fd83eddc4b396b62c33c8a637ac.png)，我们就会发现 softmax 回归器预测其中一个类别的概率为 ![\textstyle \frac{1}{ 1  + e^{ (\theta')^T x^{(i)} } }](http://deeplearning.stanford.edu/wiki/images/math/b/5/6/b56e13a98d2fe77d4bc5fd0c004befed.png)，另一个类别概率的为 ![\textstyle 1 - \frac{1}{ 1 + e^{ (\theta')^T x^{(i)} } }](http://deeplearning.stanford.edu/wiki/images/math/4/e/3/4e3deb50277418cc3eb445aa9aa7ac46.png)，这与 logistic回归是一致的。

# SR与LR的使用场合

SR与LR的使用场合区别在于，分类的对象是否互斥。举个例子：以前世界上只有男性、女性和阉人，那么我们就可以用SR来做分类，因为三者是互斥的，不可能有人既是男人又是女人，或者既是男人又是阉人。

但是，若是依照奥巴马政府在“跨厕法”中提出的心理性别标准，即你生下来是男的，即使现在心理上认为自己是女性，就可以进女厕，反之亦然。那么一个人既可以是男性的女性，也可以是女性的男性，可能这个人前一段时间觉得自己是女性，后来又觉得自己是男性了。现在的类别包括：男性、女性、女性的男性、男性的女性，这四者不再互斥，那么我们就只能用4个LR来作区分。



# Reference：
[Softmax回归](http://deeplearning.stanford.edu/wiki/index.php/Softmax回归)
[理解softmax分类器](http://scientificrat.com/2018/06/21/理解softmax分类器/)
