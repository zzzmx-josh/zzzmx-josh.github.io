---
layout: windows
title: Xshell远程服务器本地可视化方法
date: 2023-04-23 10:39:16
tags: linux
---



# 问题

在大多数时候，我们连接的远程服务器没有图形界面，不能支持浏览器功能，这时我们需要建立本地服务器与远程服务器的通信。

以Tensorboard的本地可视化方法为例：



# 不推荐：

网上常见的方法：

在本地计算机上，打开一个新的终端窗口，并使用以下命令创建一个SSH隧道：

```linux
ssh -L 6006:localhost:6006 username@remote\_server\_ip
```

其中，username是您在远程服务器上的用户名，remote\_server\_ip是远程服务器的IP地址。

1.在远程服务器上启动TensorBoard：

```linux
tensorboard --logdir=/path/to/logs
```

2.找到TensorBoard启动时显示的URL，类似于以下内容：

```linux
TensorBoard 1.14.0 at [http://localhost:6006/](http://localhost:6006/) (Press CTRL+C to quit)
```

3.在本地计算机上，打开Web浏览器并访问以下URL：

[http://localhost:6006](http://localhost:6006)



这个方法我尝试了多次，都不成功：

![](image_1.44605818.png)

<strong style="color:#00b050;">可能windows电脑不适用</strong>



# 推荐方法：Xshell隧道

## 配置

打开Xshell，右击相应的会话，在弹出的对话框中选择属性->连接->SSH->隧道->添加。

在侦听端口和目标端口中填入相同的端口，此处填写了6006;（这里的端口号，也可以随便换成其他的，只要保证两处相同即可）。

![](image_2.73d50b2e.png)

## 运行

服务器端启动tensorboard

```linux
tensrboard --logdir=log地址 --port=6006
```

本地浏览器查看

然后再本地浏览器中输入：http://127.0.0.1:6006 或者localhost:6006，可以通过tensorboard查看目前的训练情况。

![](image_3.5e37b6bd.png)

