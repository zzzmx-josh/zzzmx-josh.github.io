---
title: linux安装onedrive
date: 2023-04-24 10:43:06
tags: linux
---

# 问题

onedrive作为一个能跨平台的云盘，使用起来特别香，最近还增加了增量备份、查看历史版本等功能，特别适合有多个设备、需要频繁传输数据的使用者。

我经常需要在Linux平台保存数据，将处理的结果传输到win平台上，所以在服务器上也安装了OneDrive，主要的原理是磁盘挂载，相当于起到“扩大”磁盘空间的作用。

以contos7为例，仓库链接：[onedrive](https://github.com/skilion/onedrive)



# 安装依赖

需要的依赖：libcurl-devel，sqlite-devel, 编译工具dmd

如果有root权限，可以直接通过：

```shell
sudo yum install libcurl-devel
sudo yum install sqlite-devel
```

如果没有root权限，通过下载源代码编译，不过需要把这些动态库的路径添加到bashrc里面，具体可以参考[无root权限下解决编译时的依赖问题](https://www.jianshu.com/p/da92ca36a220/) ：

安装有先后

## openssl

```shell
wget https://www.openssl.org/source/openssl-1.0.2m.tar.gz
tar -zxvf openssl-1.0.2m.tar.gz && cd openssl-1.0.2m
#添加shared生成动态库
./config --prefix=$HOME/usr shared
make && make install
卸载使用 make clean
```

## libssh2

```shell
wget https://www.libssh2.org/download/libssh2-1.8.0.tar.gz
tar -zxvf libssh2-1.8.0.tar.gz && cd libssh2-1.8.0
./configure --with-libssl-prefix=$HOME/usr/ssl --prefix=$HOME/usr
make && make install
```

## libcurl

```shell
wget https://curl.haxx.se/download/curl-7.56.1.tar.gz
tar -zxvf curl-7.56.1.tar.gz && cd curl-7.56.1
./configure --prefix=$HOME/usr --enable-http  --enable-https --enable-ftp --enable-file --enable-proxy --enable-telnet --enable-libcurl-option --enable-ipv6 --with-lib --with-ssl
make && make install
```

安装成功后记得把路径加入到环境变量，

将以上内容加入到.bashrc中：

```shell
export LD_LIBRARY_PAHT=$LD_LIBRARY_PATH:$HOME/usr/lib64
export LD_LIBRARY_PAHT=$LD_LIBRARY_PATH:$HOME/usr/lib
export PKG_CONFIG_PATH=$HOME/usr/lib/pkgconfig:$HOME/usr/share/pkgconfig
export PATH=$HOME/usr/bin:$PATH
```

使设置生效：

```shell
source ~/.bashrc
```



## 最后安装dlang

```shell
wget https://dlang.org/install.sh 
bash install.sh install dmd-2.080.0
```

我安装的版本是dmd\-2.080.0，安装最新的程序后面会报错

安装成功后：

![](image_1.8ec48fc8.png)



使用 `source ~/dlang/dmd-2.080.0/activate` 命令可进入环境

注意：onedrive安装过程在进入环境后才进行的，安装完再退出环境。



# 安装onedrive

## 下载

```shell
wget https://github.com/abraunegg/onedrive/archive/refs/tags/v2.3.4.tar.gz
tar zxvf v2.3.4.tar.gz
cd onedrive-2.3.4/
```

## 修改配置

由于不是使用root命令安装, 所以需要修改makefile.in

```shell
vim makefile.in
```

1. 加入日志文件路径

![](image_2.49f672c6.png)

2. 更改用户

![](image_3.1df847f1.png)



将此处的 -o root -g users 改成 -o 用户名，删除后面的\-g users

并且将下一行的onedrive 后面的${DESDIR}删除



## 安装

```shell
 ./configure --prefix=$HOME/usr && make && make install
```

安装完，用deactivate 退出环境



# 使用

## 授权

现在您需要向 Microsoft 授权 Onedrive，以便它可以访问您的帐户。在终端中输入以下内容：

```shell
 onedrive
```

![](image_4.c5e19314.png)

它会提示您访问该 URL 以获得授权。

复制此链接，在windows浏览器中打开，您将看到一个空白页面。复制 URL 并在提示符下将其粘贴到终端中。

登录您的 OneDrive 帐户，并复制此处的链接粘贴到终端。

完成以上步骤后，Onedrive 将开始将云中的所有文件下载到本地文件夹。

## 同步配置

您可以在 Onedrive git 文件夹中找到“config”文件。要激活，请将其移至 ~/.config/onedrive/文件夹。

```shell
mkdir -p ~/.config/onedrive/
cp ~/onedrive/config ~/.config/onedrive/
```

打开配置文件。您可以配置两个选项：“sync\_dir”和“skip\_files”。

sync\_dir：存储 OneDrive 文件的位置。放置在此文件夹中/从中删除的所有文件，都将同步到云端。

skip\_files：不同步的文件类型（或文件模式）。

完成更改后，保存并重新启动 Onedrive。

具体配置请参考[linux onedrive配置](https://www.jianshu.com/p/017aa8ceef02/)



