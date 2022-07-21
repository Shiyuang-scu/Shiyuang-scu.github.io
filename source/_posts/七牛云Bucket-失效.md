---
title: 七牛云Bucket失效
date: 2018-11-22 06:27:09
tags: 世界之内
---

很讨厌，今天照例写博客传图片，发现七牛云的图床失效了，刚开始还以为是网站出错了，后来查了一下出错信息，是因为七牛云收回了测试域名，要求每个域名都要有备案。最难受的是，Bucket失效后，我无法下载原来放在七牛云中的图片，我也没有把图片备份。这样看起来，我博客中所有的图都找不回了，怎么办？
<!--more-->
后来我还是查了一下，找到了解决方案：

在查阅了相关资料时发现有qshell和qrsctl两种办法，因为不想再申请七牛云的空间了，所以选择了使用qrsctl下载七牛云空间中的文件。

>qrsctl 是根据七牛云存储API实现的一个简易命令行辅助工具。覆盖七牛云存储开发者网站包含的大部分甚至更高级的功能。开发者在对七牛云存储 API 有基本了解的情况下，此工具将会非常适用。

我下载后的文件名字是，qrsctl.dms，刚开始可能会有权限问题，chmod +x ./qrsctl.dms赋予可执行权限。直接在terminal中运行./qrsctl.dms会出现相应说明文档。

按照下面的指令，输入自己七牛云的账号和密码登录：

```sh
./qrstcl.dms login <your username> <your password>
```

接下来，我们列出我们所有的空间（buckets）：

```sh
./qrsctl.dms buckets
```

可以看到我有一个空间：blog

![QiNiuYunFix1](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/QiNiuYunFix1.png)
我直接把文件拖进控制台，所以命令行部分是文件的绝对路径，而不是./qrsctl.dms，这本质都是一样的，你也可以输入绝对路径。
现在我们通过命令行看看blog里有什么文件：

```
./qrsctl.dms listprefix outmusic ''
```
注意后两个是英文的两个单引号

结果就是我blog中的文件名。

![QiNiuYunFix2](https://raw.githubusercontent.com/Shiyuang-scu/blog_img/master/QiNiuYunFix2.png)

当然要注意第一行是七牛云自带的一个标志字符串"marker:"。

得到图片名后，可以通过七牛云的如下命令下载一个文件。

```sh
qrsctl.dms get <Bucket> <Key> <DestFile>
拿我做例子，就是：
./qrsctl.dms get blog 1.tiff ./1.tiff
```

就可以将blog中的1.tiff下载到电脑当前路径下，保存成的1.tiff，当然也可以改成其他名字。

以上几步综合起来，我们就可以通过批处理把所有内容下载到本地了。

下面是批处理代码：

```sh
#!/bin/bash
files=`./qrsctl.dms listprefix blog ''`
i=0
echo $files | tr " " "\n" | while read line
do
    if(($i>0))
    then
        echo $line
        ./qrsctl.dms get blog $line ./$line
    fi
    i=$(($i+1))
done
```
完美解决了问题。

我已经把图片转移到github，并且将图片备份到百度云，吃一堑长一智，当初我确实考虑得太少，以后做事还要更谨慎。

修改了图床后，我要把每一篇博客的图片路径从七牛云的路径改成github的路径，太费力了。也许我可以做个正则，然后批处理？我没做，因为改四十多篇文章的图片路径肯定比我编程的时间短，🐶，但是如果是上百篇文章……但愿不要有让我做批处理的机会了！！！

另外，七牛云做得确实不厚道。