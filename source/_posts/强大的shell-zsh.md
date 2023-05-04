---
title: 强大的shell-zsh
date: 2023-05-01 17:47:03
tags: linux
---

# 介绍

zsh或许可以认为是最好的shell，对于我这种记不住终端命令的人来说简直是神器。自从从bash转zsh之后，使用Linux命令直接起飞，非常得心应手。zsh相比较bash所具有的特点：

1\. 更好的命令补全功能：zsh的命令补全功能比bash更强大和智能，支持更多的选项和自动提示。

2\. 自定义主题和插件：zsh提供了更多的主题和插件，可让用户轻松自定义命令提示符和命令别名等。

3\. 更好的历史记录管理：zsh支持更多的历史记录功能，包括事件时间戳、时间轴浏览、模糊搜索等。

4\. 更高效的配置管理：zsh的配置文件可以使用zsh本身脚本语言而不仅仅是bash语法。

5\. 其他特性包括补全脚本、自动文件名扩展、定时命令等，都只能在zsh中使用。



# 安装

mac默认的shell就是zsh，但是linux上需要用户自行安装，下面介绍的是没有root命令该如何从源代码安装：

## 安装依赖ncurses

```shell
wget ftp://ftp.invisible-island.net/ncurses/ncurses.tar.gz && tar -zxvf ncurses.tar.gz
./configure --enable-shared --prefix=$HOME/usr
make && make install
```

## 安装zsh

```shell
wget -O zsh.tar.gz https://sourceforge.net/projects/zsh/files/latest/download
tar -zxvf zsh.tar.gz && cd zsh
export CPPFLAGS="-I$HOME/usr/include/" LDFLAGS="-L$HOME/usr/lib"
./configure --prefix=$HOME/usr --enable-shared
make && make install
```

# zsh配置

zsh自定义配置，可供选择的插件以及主题非常多，因此一定要搭配oh-my-zsh。

## 安装oh-my-zsh

```shell
#快速安装
sh -c "$(curl -fsSL https://gitee.com/mirrors/oh-my-zsh/raw/master/tools/install.sh)" 

#手动安装
# 从github上克隆oh-my-zsh，如果克隆失败，可能是网络问题，多尝试问题
git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
# 用oh-my-zsh的zsh配置文件替代
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zhsrc


# 安装一些字体, 不然一些主题会显示异常
cd src
git clone https://github.com/powerline/fonts.git --depth=1
cd fonts && ./install.sh
```

重启一下终端，后面根据需要调整配置文件里的参数。



## 安装插件

oh my zsh 项目提供了完善的插件体系，相关的文件在~/.oh-my-zsh/plugins目录下，默认提供了100多种，只要打开相关目录下的 zsh 文件看一下就知道了。插件也是在.zshrc里配置，找到plugins关键字，就可以加载自己的插件了，系统默认加载 git，可以在后面追加内容，如下：

plugins=(git autojump )



### 路径任意跳转auto jump

```shell
cd ~/.oh-my-zsh/custom/plugins/ 
 git clone https://github.com/wting/autojump wting/autojump
cd wting/autojump
./install.py
```

执行命令后，把屏幕上对应的文字添加bashrc中。

重新加载环境变量就可以用 j \[各种短写路径\] 跳转, tab 键提示有奇效

用法：输入 j 目录名 或 j 目录名包含的字符（这个目录必须是之前 cd 访问过的），就可直接切换到相应的目录。不用再各种cd啦～



### 自动提示命令zsh-autosuggestions

```shell
# 克隆仓库到本地 ~/.oh-my-zsh/custom/plugins 路径
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
#在~/.zshrc 中添加
plugins=(
  zsh-autosuggestions
)
```

如果看不到变化，可能是字体颜色太淡

```shell
 vim ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
```

修改 ZSH\_AUTOSUGGEST\_HIGHLIGHT\_STYLE='fg=10'



### 语法高亮zsh-syntax-highlighting

```shell
$ git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
#在~/.zshrc 中添加
plugins=(
zsh-syntax-highlighting
)
```



我目前就安装了这些插件，已经非常方便。



# 设置zsh为默认shell

为了将默认shell更改为zsh，需要在终端中运行以下命令：

```shell
echo "export PATH=$HOME/usr/bin:$PATH" >> ~/.bashrc
echo "exec $HOME/usr/bin/zsh -l" >> ~/.bashrc
source ~/.bashrc
```

我的zsh安装在$HOME/usr/bin目录下，也可以换成其他路径。上述命令将zsh安装的路径添加到`~/.bashrc` 文件中的 PATH 环境变量中，启动 zsh 并将其设置为默认shell。

退出当前终端并重新打开一个新终端，验证默认shell是否更改为zsh。

```shell
echo $SHELL
```
