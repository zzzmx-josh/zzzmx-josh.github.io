---
title: python制作图片ppt
date: 2023-04-24 15:54:25
tags: python
---

# 问题：

当需要有大量类似的图片结果需要分析的时候，一个一个插入ppt比较费时间，而且万一顺序错了就是灾难，python的Pillow库可以读取和操作图片，而python-pptx库可以创建PPT文件，非常方便。

# 库安装

```shell
 pip install python-pptx
```

# 单个目录

```python
from pptx import Presentation
from pptx.util import Inches
from PIL import Image
import os
# 创建一个新的PPT文件
prs = Presentation()
# 图片文件夹路径
img_folder = 'path'
# 定义每页显示的图片数量
pics_per_page = 2
prs = Presentation()
# 获取ppt大小
width = prs.slide_width
height = prs.slide_height
# 图片大小缩放
rescale = 1 
# width = Inches(6)
# height = Inches(4.5)

# 遍历文件夹中的所有图片
pic_files = sorted(os.listdir(img_folder))
for i in range(0, len(pic_files), pics_per_page):
    # 创建一个新的幻灯片
    slide = prs.slides.add_slide(prs.slide_layouts[6])
    # 在幻灯片中添加图片
    for j in range(pics_per_page):
        if i + j >= len(pic_files):
            break
        # 打开图片文件
        img_path = os.path.join(img_folder, pic_files[i + j])
        img = Image.open(img_path)
        width_, height_ = img.size
        # 获取图片长高比例
        ratio = height_ / width_
        # 计算图片在幻灯片中的位置和大小
        # left = Inches(j * 5)
        left = 0
        top = j * height / 2
        width_rescale = width * rescale
        height_rescale = width_rescale * ratio
        
        # 将图片添加到幻灯片中
        pic = slide.shapes.add_picture(img_path, left, top, width=width_rescale, height=height_rescale)
# 保存PPT文件
prs.save('output.pptx')
```

说明：

这段代码首先创建了一个新的PPT文件，然后遍历指定文件夹中的所有图片文件。对于每两个图片文件，它创建一个新的幻灯片，并将两张图片添加到幻灯片中。最后，它将PPT文件保存到指定的输出文件中。请注意，这里使用了sorted()函数来确保图片按照文件名的字母顺序排序。

在幻灯片中，每张图片的宽和ppt大小保持一直，高度按照比例缩小，每张ppt上下排列两张图片，可以根据需要调整这些值。



add\_picture后的四个参数分别为图片放置的左上坐标，以及宽度和高度，坐标如下：

![image-20230424155501482](image-20230424155501482.png)

参数说明：

往右，left变大，往下，top变大



🌟注意： 图片的size为像素单位，注意和Inches之间的换算



# 多个目录

如果有多个目录，每个文件夹下面的图片单独保存到ppt中也非常简单，只需要再嵌套一层for循环，这里我使用了pathlib库[教程](https://zhuanlan.zhihu.com/p/139783331/) ，操作路径比os库方便

```python
from pptx import Presentation
from pptx.util import Inches
from PIL import Image
import os
from pathlib import Path
from pprint import pprint

# 图片文件夹路径
path= Path('testFlag')
# 匹配子路径
pathlists = list(path.glob("Loop*"))
# 确认路径
pprint(pathlists)

for img_folder in pathlists:
  
  # 创建一个新的PPT文件
  prs = Presentation()

  # 定义每页显示的图片数量
  pics_per_page = 2

  prs = Presentation()
  width = prs.slide_width
  height = prs.slide_height
  rescale = 1 
  # width = Inches(6)
  # height = Inches(4.5)


  # 遍历文件夹中的所有图片
  pic_files = sorted(img_folder.glob("*"))

  for i in range(0, len(pic_files), pics_per_page):
      # 创建一个新的幻灯片
      slide = prs.slides.add_slide(prs.slide_layouts[6])

      # 在幻灯片中添加图片
      for j in range(pics_per_page):
          if i + j >= len(pic_files):
              break

          # 打开图片文件
          img_path = os.path.join(img_folder, pic_files[i + j])
          img = Image.open(img_path)
          width_, height_ = img.size
          ratio = height_ / width_

          # 计算图片在幻灯片中的位置和大小
          # left = Inches(j * 5)
          left = 0
          top = j * height / 2 + Inches(0.5)

          width_rescale = width * rescale
          height_rescale = width_rescale * ratio
          
          # 将图片添加到幻灯片中
          pic = slide.shapes.add_picture(img_path, left, top, width=width_rescale, height=height_rescale)

  # 保存PPT文件
  prs.save(img_folder.parent.joinpath(img_folder.name +".pptx"))
```

