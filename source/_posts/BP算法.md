---

title: 【机器学习】BP算法
date: 2018-05-26 21:17:36
tags: 世界之内
---

断断续续零零碎碎地学机器学习半年了，不会实践，不会运用，只是粗略地学了理论基础，因为涉及很多数学公式，markdown的数学公式语法又挺麻烦的，所以一直没有写过机器学习算法的文章。最近恰逢创新班、双特生面试，因为我是以机器学习为特点介绍我自己的，所以看了些算法的具体理论和优缺点。今天下午看了2个小时的BP算法，心想不如在近纲课上写一篇BP算法的文章。

# 一般思想

首先，依然是梯度下降法，为了更新$W$和$b$，就要计算出代价函数$J$对两者的偏导数，即

$\frac{\partial}{\partial W_{ij}^{(l)}} J(W,b; x, y) = a^{(l)}_j \delta_i^{(l+1)}$
$\frac{\partial}{\partial b_{i}^{(l)}} J(W,b; x, y) = \delta_i^{(l+1)}$

其中，

$\delta^{(l)}_i=\frac{\partial J(W,b;x,y)}{\partial z_i^{(l)}}=\frac{\partial }{\partial z^{(l)}_i}\frac{1}{2} \left\|y - h_{W,b}(x)\right\|^2 = - (y_i - a^{(l)}_i)\cdot f'(z^{(l)}_i)$

<!--more-->

但这两个公式只能够更新输出层与前一层连接线的权重和输出层的偏置，原因是因为 δ 值依赖了真实值y这个变量，但是我们不可能知道每层隐藏层的真实值，所以无法计算每层隐藏层的 δ 值。而BP算法则可以让我们利用后一个δ计算前一个δ，避免了使用真实值。

# BP算法细节

1: 首先当然是正向计算咯，分别求出$L2,L3$,...直至最后一层$Ln$的**激活值**。我们这里把输入当做第一层。下面是真正的反向传播。

2: 对于第n层（最后一层是特殊的，必须单独拿出来）每个输出单元$i$,下面的$l$的值为$n$, 计算每个结点的残差：

$\delta^{(l)}_i=\frac{\partial J(W,b;x,y)}{\partial z_i^{(l)}}=\frac{\partial}{\partial z^{(l)}_i} \;\;\frac{1}{2} \left\|y - h_{W,b}(x)\right\|^2 = - (y_i - a^{(l)}_i) \cdot f'(z^{(l)}_i)$

注意：这里最后乘上了对“net”的导，如果是用sigmoid的函数的话，根据$f′(z(l)i)=a(l)i(1−a(l)i)$, 最后一层的第$i$个结点的残差$δ^{(l)}i=−(yi−a^{(l)}i)∙a^{(l)}i(1−a^{(l)}i)$

3： 从倒数第二层开始，也就是说 $l=n−1,n−2,n−3,…,2$ 的各层,第$l$层的第$i$个结点的**残差**计算： 

$\delta^{(l)}_i = \left( \sum_{j=1}^{s_{l+1}} W^{(l)}_{ji} \delta^{(l+1)}_j \right) f'(z^{(l)}_i)$



分析：要想知道第l层的第i个结点的残差，必须知道该节点所连接的下一层的各个结点的权值，以及这些结点的残差，幸亏第l+1层已经计算出来了残差，你只要把后面一层的每个结点j的残差乘以该结点与这一层的结点i相连的权值，然后加和，最后别忘了乘以这一层的激活方式的导数。

4: 你可能会说要残差干嘛？当然是计算$∂J/∂w$和$∂J/∂b$用的。 
只要：

$\frac{\partial}{\partial W_{ij}^{(l)}} J(W,b; x, y) = a^{(l)}_j \delta_i^{(l+1)}$

$\frac{\partial}{\partial b_{i}^{(l)}} J(W,b; x, y) = \delta_i^{(l+1)}$

**结论：求J对“结点j到i的线路”的导数，求出后者i的残差，然后乘以这条线路的流量即可。**

分析：其实是这样的，Wlij是第l层到l+1层的权值，并且是从结点j到结点i的权值。根据链式法则： 

$ \frac{\partial J{(W,b; x, y)}}{\partial w_{ij}^{(l)}} = \frac{\partial J{(W,b; x, y)}}{\partial out_{i}} * \frac{\partial out_{i}}{\partial net_{i}} * \frac{\partial net_{i}}{\partial w_{ij}}$

 

残差的定义就是$\frac{\partial J{(W,b; x, y)}}{\partial net_{i}}$，根据链式法则： 

$ \delta_i^{(l+1）}=\frac{\partial J{(W,b; x, y)}}{\partial net_{i}} = \frac{\partial J{(W,b; x, y)}}{\partial out_{i}} * \frac{\partial out_{i}}{\partial net_{i}}$

这下明白了吧，就是说第$l+1$层，

$ \frac{\partial J}{\partial w_{ij}^{(l)}}=\delta_i^{(l+1)}* \frac{\partial net_{i}}{\partial w_{ij}}=\delta_i^{(l+1)}a^{(l)}_j$

 注意，第$l+1$层的$\frac{\partial net_{i}}{\partial w_{ij}}$就是该层的输入，也就是第l层的输出$a^{(l)}_j$. 

**最后一个问题**，为什么

$ \delta^{(l)}_i = \left( \sum_{j=1}^{s_{l+1}} W^{(l)}_{ji} \delta^{(l+1)}_j \right) f'(z^{(l)}_i)$

呢?

$ \delta^{(l-1)}_i =\frac{\partial}{\partial z^{(l-1)}_i}J(W,b;x,y) = \frac{\partial}{\partial z^{(l-1)}_i}\frac{1}{2} \left\|y - h_{W,b}(x)\right\|^2 = \frac{\partial}{\partial z^{(l-1)}_i}\frac{1}{2}\sum_{j=1}^{S_{l}}(y_j-a_j^{(l)})^2 \\ = \frac{1}{2}\sum_{j=1}^{S_{l}}\frac{\partial}{\partial z^{(l-1)}_i}(y_j-a_j^{(l)})^2 = \frac{1}{2} \sum_{j=1}^{S_{l}}\frac{\partial}{\partial z^{(l-1)}_i}(y_j-f(z_j^{(l)}))^2 \\= \sum_{j=1}^{S_{l}}-(y_j-f(z_j^{(l)})) \cdot \frac{\partial}{\partial z_i^{(l-1)}}f(z_j^{(l)}) = \sum_{j=1}^{S_{l}}-(y_j-f(z_j^{(n_l)})) \cdot  f'(z_j^{(l)}) \cdot \frac{\partial z_j^{(l)}}{\partial z_i^{(l-1)}} \\ = \sum_{j=1}^{S_{l}} \delta_j^{(l)} \cdot \frac{\partial z_j^{(l)}}{\partial z_i^{(l-1)}} = \sum_{j=1}^{S_{l}} \left(\delta_j^{(l)} \cdot \frac{\partial}{\partial z_i^{(l-1)}}\sum_{k=1}^{S_{(l-1)}}f(z_k^{(l-1)}) \cdot W_{jk}^{(l-1)}\right) \\ = \sum_{j=1}^{S_{l}} \delta_j^{(l)} \cdot  W_{ji}^{(l-1)} \cdot f'(z_i^{(l-1)}) = \left(\sum_{j=1}^{S_{l}}W_{ji}^{(l-1)}\delta_j^{(l)}\right)f'(z_i^{(l-1)})$

 我们可以看到

$ \delta^{(l-1)}_i =\left(\sum_{j=1}^{S_{l}}W_{ji}^{(l-1)}\delta_j^{(l)}\right)f'(z_i^{(l-1)})$

前一层的偏差是后一层偏差与相关权重的点积，intuitively，前一层的偏差是后一层偏差之和，符合我们的直觉。
