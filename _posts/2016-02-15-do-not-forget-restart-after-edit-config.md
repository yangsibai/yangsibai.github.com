---
layout: post
title: 修改程序配置后不要忘了重启
tags:
- server
- vps
- mongodb
---

今天服务器出了一点问题，重启之后服务都自动启动了起来，但是发现 mongodb 的数据丢了，完全没有数据。

初步怀疑是由于重启导致 mongodb 进程没有正常关闭，数据损坏了，所以查了官方文档如何恢复数据，根据 [恢复指导](http://docs.mongodb.org/manual/tutorial/recover-data-following-unexpected-shutdown) 进行了数据修复，但是还是没有找到数据。

尝试了很多办法也没有修复，后来不得不去找服务商将磁盘快照找出来，挂载上之后再进去找，希望能从备份中找到数据文件。最终也是失败了。

经过了3，4个小时的艰苦工作，最后发现的问题令人哭笑不得，数据文件位置错了。

由于我的系统盘比较小，所以安装了 mongodb 之后我修改了配置文件，将 `dbpath` 设置到了另外一块盘上，**但是我忘了重启 mongodb** 。
最后的结果就是新的配置没有生效，数据一直写到了默认的路径下面(CentOS 上是 `/var/lib/mongo`)。
服务运行一直很稳定，几个月也没有重启过，今天重启后就加载了新的配置，在新的路径下面找不到数据。
而且我是在启动了应用程序之后发现的数据丢失，我为了验证 mongodb 是否连接有问题，又创建了新的数据，导致新的路径下面文件看起来很正常，所以严重影响了问题定位。

我一直用英文的关键词去搜索数据恢复，数据丢失，Google 出来的文章都是关于数据怎么恢复的。*似乎 mongodb 真的容易丢数据* 最后使用中文的关键词查出来了相似问题，并得到了解决。还是使用母语对问题的描述更加精准一些。

从这个事情得到的最重要的一条教训就是：

**修改了程序配置后不要忘了重启应用新的配置**

每次故障处理总是能得到感悟：

+ 重点要查看程序的日志
+ 不要迷恋英文文章
+ 做好数据备份!
+ 重大问题要果断切断服务，防止产生更大的影响
+ 慎重选择不熟悉的技术

最后就是希望 Google 能早点回来。
