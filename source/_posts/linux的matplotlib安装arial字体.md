---
title: linux的matplotlib安装arial字体
date: 2023-04-23 16:46:35
tags: python, linux
---

# 问题

绘图中经常会使用Arial字体，linux服务器上默认是没有这个字体。为了省去每次修改字体的麻烦，在matplotlib中安装了这个字体，并且设置为matplotlib默认字体。



# 流程

## 复制字体至 matplotlib 库 fonts/ttf 目录

windows上有Arial字体，在C:\\Windows\\Fonts 路径下，

![](image_1.b2ef3533.png)



找到并且全部复制到linux服务器上matplotlib字体库下面，

路径为：python\_envs\_name /lib/python3.9/site-packages/matplotlib/mpl-data/fonts/ttf

![image-20230423165253522](image-20230423165253522.png)

## 修改 matplotlib 库配置文件 matplotlibrc

路径：env\_name/lib/python3.9/site-packages/matplotlib/mpl-data/matplotlibrc

打开后查找 font.family，将Arial加到最前面：

```shell
#font.serif: Arial, DejaVu Serif, Bitstream Vera Serif, Computer Modern Roman, New Century Schoolbook, Century Schoolbook L, Utopia, ITC Bookman, Bookman, Nimbus Roman No9 L, Times New Roman, Times, Palatino, Charter, serif

#font.sans-serif: Arial,DejaVu Sans, Bitstream Vera Sans, Computer Modern Sans Serif, Lucida Grande, Verdana, Geneva, Lucid, Arial, Helvetica, Avant Garde, sans-serif

#font.cursive: Apple Chancery, Textile, Zapf Chancery, Sand, Script MT, Felipa, Comic Neue, Comic Sans MS, cursive

#font.fantasy: Chicago, Charcoal, Impact, Western, Humor Sans, xkcd, fantasy

#font.monospace: DejaVu Sans Mono, Bitstream Vera Sans Mono, Computer Modern Typewriter, Andale Mono, Nimbus Mono L, Courier New, Courier, Fixed, Terminal, monospace
```



## 清除 matplotlib cache

需要清除原来的matplotlib缓存修改的字体才会生效，最直接的办法就是把缓存文件夹直接删除

```shell
rm -rf /home/User/.cache/matplotlib
```



# 字体说明

```python
font.family : sans-serif

font.sans-serif : SimHei

axes.unicode_minus : False
```



1. <strong style="color:#00b0f0;">sans-serif</strong>：专指西文中无衬线的字体，与汉字字体中的黑体相对应。该类字体通常是机械的和统一线条的，它们往往拥有相同的曲率，笔直的线条，锐利的转角。这种字体当前系统中肯定存在，所以使用这个字体一定能显示出来，所以通过会加上sans-serif来保证调用。

   常见的无衬线字体有 Trebuchet MS, Tahoma, Verdana, Arial, Helvetica, 中文的幼圆、隶书等等。

   font-family最后加上sans-serif，也是为了保证能够调用这个字体族里面的字体，因为大多数计算机里都有这种字体。

   

2. 其他字体

- 黑体：SimHei

- 宋体：SimSun

- 微软雅黑体：Microsoft YaHei




