---
title: vscode-basic
date: 2023-04-20 15:42:18
tags: vscode
---



# 下载安装

打开[Vscode](https://code.visualstudio.com/) 官网直接下载即可

#  插件

1. Python

   Python为VSCode添加了对Python的语言支持，包括 IntelliSense 和Debugging等功能

 ![image-20230420145247297](image-20230420145247297.png)

2. Jupyter

     Jupyter为VSCode添加了对Jupyter Notebook的功能支持

 ![image-20230420145530009](image-20230420145530009.png)

3. Code Runner

   Code Runner用于直接运行多种语言的代码片段或文件

 ![image-20230420145551529](image-20230420145551529.png)

4. Better Comments

   Better Comments为代码注释提供各种特定类型注释的高亮。

 ![image-20230420145609253](image-20230420145609253.png)

5. Bracket Pair Colorizer

   给匹配的括号着色

6. Indent-Rainbow

   让缩进带有颜色

7. Path Intellisense

   自动完成文件名

8. Prettier - Code formatter

   更优雅的代码格式化,vscode 里比较优秀的一个格式化插件

9. matlab插件

   支持自动补全、跳转定义、变量重命名 

10. Remote-SSH

 ![image-20230420145707498](image-20230420145707498.png)

11. Markdown Preview Enhanced（markdown 预览增强）

12. WD-TabNine

非常强大的代码自动补全工具



# Remote-SSH连接

## 配置

安装Remote-SSHR之后，点击界面左下角的打开远程窗口

 ![image-20230420145744807](image-20230420145744807.png)

选择Connect to Host

 ![image-20230420145758242](image-20230420145758242.png)

选择Add New SSH Host

 ![image-20230420145807767](image-20230420145807767.png)

输入对应的用户名和远程IP地址

 ![image-20230420145813675](image-20230420145813675.png)

在弹出的设置配置文件路径，选择第一项

 ![image-20230420145819225](image-20230420145819225.png)

界面右下角显示Host added!

 ![image-20230420145824770](image-20230420145824770.png)

##  连接

添加host成功之后，再点击左下角的打开远程连接窗口，

  ![image-20230420145829467](image-20230420145829467.png)

就会出现刚刚配置好的host，点击，输入密码，就可以成功登录了

 ![image-20230420145837776](image-20230420145837776.png)

## 配置免密登录

登录成功后，为了避免每次输入密码，在linux服务器上添加公钥（参考添加公钥到github）;

在远程终端上

先定位目录 
` cd .ssh`

再看看有没有authorized_keys文件：
 `ls`

然后编辑它
` vim authorized_keys`

然后按i进入编辑模式
 然后把本机 后缀.pub文件（比如id_rsa.pub）的内容粘贴上去：
 ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDPsqZlc1lASXIwrOSpyyghF+U 一大串

最后 按ESC 然后输入:wq （写入内容并quit退出）
 然后关闭vscode窗口自动断开链接，再按 第3步 直接点击 然后直接连接上并成功登录！



# 使用远程服务器上的jypyter

## 启动jupyper 服务器

在终端激活对应的conda 虚拟环境之后，输入jupyter notebook，启动jupyter服务器

 ![image-20230420145849003](image-20230420145849003.png)

复制下面的地址，两个都可以

   ![image-20230420145854628](image-20230420145854628.png)

##  vscode中连接远程jupyter

安装juyter拓展后，按快捷键打开输入框，输入jupyter以后就可以看到以下提示，选择为连接指定jupyter服务器

 ![image-20230420145901403](image-20230420145901403.png)

之后进入以下界面，需要输入刚刚已启动的jupyter notebook的URL连接，如上的两个链接，复制输入框中按回车就可以连接服务器上的jupyter

 ![image-20230420145907727](image-20230420145907727.png)

此时就可以选择新建或打开远程服务器上的ipynb文件，可能会提示要为远程服务器安装python拓展，确认安装即可。

##  切换虚拟环境

服务器上配置有多个 [conda](https://so.csdn.net/so/search?q=conda&spm=1001.2101.3001.7020) 虚拟环境，如果每次启动jupyter之前需要激活对应的虚拟环境，后续就不方便切换，

使用nb_conda_kernels 添加所有环境

```shell
conda activate base   # could be also some other environment

conda install nb_conda_kernels

jupyter notebook
```

安装好后，打开 jupyter notebook 就会显示所有的 conda 环境

 ![image-20230420145917965](image-20230420145917965.png)
