---
title: python多进程处理数据
date: 2023-04-25 08:07:46
tags: [python, multiprocess]
---

# 多线程、多进程

进程(multiprocess)是操作系统资源分配（内存，显卡，磁盘）的最小单位，线程(Thread)是执行调度（即cpu调度）的最小单位（cpu看到的都是线程而不是进程），一个进程可以有一个或多个线程，线程之间共享进程的资源。

cpu物理核数实际上是可以同时并行的线程数量，由于超线程技术，部分cpu实际上可以并行的线程数量通常是物理核数的两倍，这也是操作系统看到的核数。

处理任务可以分为计算密集型和IO密集型，假设使用一个进程来完成这个任务，对计算密集型任务，可以使用【<strong style="color:#00b050;">核心数</strong>】个线程，就可以占满cpu资源，进而可以充分利用cpu，如果再多，就会造成额外的开销；对于IO密集型任务（涉及到网络、磁盘IO的任务都是IO密集型任务），线程由于被IO阻塞，如果仍然用【核心数】个线程，cpu是跑不满的，于是可以使用<strong style="color:#00b050;">更多个线程</strong>来提高cpu使用率。



# python中的并行

对于其他语言，多核CPU支持多个线程同时执行。但在Python中，无论是单核还是多核cpu，一个进程同一时间只能由一个线程在执行。其根源是 <strong style="color:#00b050;">GIL（Global Interpreter Lock 全局解释器锁)</strong> 的存在。

设计原因：多线程会共享进程中的地址空间和数据空间，一个线程的数据可以直接提供给其他线程使用，特别容易造成变量值的混乱，所以通过线程锁来限制线程的执行，保护数据安全。某个线程想要执行，必须先拿到 GIL，在一个 Python 进程中，GIL 只有一个。拿不到通行证的线程，就不允许进入 CPU 执行。所以多线程在python中是行不通的。python中的并行主要指的是进程并行，

通过调用 Python 自带的多进程库 `Multiprocessing`,可以轻松的在本地电脑上进行多核并行计算



# multiprocessing

这个模块主要有以下两个部分：

## multiprocess.Process

### 接口

multiprocess.Process([group [, target [, name [, args [, kwargs]]]]])，由该类实例化得到的对象，表示一个子进程中的任务（尚未启动）

#### 参数：

- group参数未使用，值始终为None
- target表示调用对象，即子进程要执行的任务
- args表示调用对象的位置参数元组，args=(1,2,'egon',)
- kwargs表示调用对象的字典，kwargs={'name':'egon','age':18}
- name为子进程的名称

注意：

1. 需要使用关键字的方式来指定参数
2. args指定的为传给target函数的位置参数，是一个元组形式，必须有逗号

### 方法

- p.start()：启动进程，并调用该子进程中的p.run()
- p.run()：进程启动时运行的方法，正是它去调用target指定的函数，我们自定义类的类中一定要实现该方法
- p.terminate()：强制终止进程p，不会进行任何清理操作，如果p创建了子进程，该子进程就成了僵尸进程，使用该方法需要特别小心这种情况。如果p还保存了一个锁那么也将不会被释放，进而导致死锁
- p.is_alive()：如果p仍然运行，返回True
- p.join([timeout])：主线程等待p终止（强调：是主线程处于等的状态，而p是处于运行的状态）。timeout是可选的超时时间，需要强调的是，p.join只能join住start开启的进程，而不能join住run开启的进程



## multiprocessing.Pool()

当被操作对象数目不大时，可以直接利用multiprocessing中的Process动态成生多个进程，十几个还好，但如果是上百个，上千个目标，手动的去限制进程数量却又太过繁琐，此时可以发挥进程池的功效。

Pool可以提供指定数量的进程供用户调用，当有新的请求提交到pool中时，如果池还没有满，那么就会创建一个新的进程用来执行该请求；但如果池中的进程数已经达到规定最大值，那么该请求就会等待，直到池中有进程结束，才会创建新的进程来它。

### 接口

multiprocessing.pool.Pool(processes=None, initializer=None, initargs=(), maxtasksperchild=None, context=None)

#### 参数：

- processes — 进程池中进程数量，如果为 None，则使用 os.cpu_count() 返回的值
- initializer — 如果该参数不为 None，则所有进程池中的进程启动时都会先执行 initializer(*initargs)
- maxtasksperchild — 如果该参数不为 None，则进程在执行 maxtasksperchild 次任务后会被自动销毁、重启
- context — 用于指定进程池中进程运行的上下文

### 方法：

- 1、apply() — 该函数用于传递不定参数，主进程会被阻塞直到函数执行结束（不建议使用，并且3.x以后不再出现），函数原型如下：
- apply(func, args=(), kwds={})

- 2、apply_async — 与apply用法一致，但它是非阻塞的且支持结果返回后进行回调，函数原型如下：

- apply_async(func[, args=()[, kwds={}[, callback=None]]])

- 3、map() — Pool类中的map方法，与内置的map函数用法基本一致，它会使进程阻塞直到结果返回，函数原型如下：

- map(func, iterable, chunksize=None)

- 注意：虽然第二个参数是一个迭代器，但在实际使用中，必须在整个队列都就绪后，程序才会运行子进程。

- 4、map_async() — 与map用法一致，但是它是非阻塞的。其有关事项见apply_async，函数原型如下：

- map_async(func, iterable, chunksize, callback)

- 5、close() — 关闭进程池（pool），使其不在接受新的任务。

- 6、terminal() — 结束工作进程，不在处理未处理的任务。

- 7、join() — 主进程阻塞等待子进程的退出， join方法要在close或terminate之后使用。



# 例子

我主要用多进程来处理数据、画图，下面是一个基本的模板:

```python
# 导入模块
from pathlib import Path
import matplotlib.pyplot as plt
import numpy as np                      
import datetime
import multiprocessing as mp


# 定义处理函数
def single_worker(file_paths):
    # 处理文件对应的路径的数据
    data = []
    for file_path in file_paths:
	
        with open(file_path, 'r') as f:
            data.append(f.read())

	# 画图
    plt.plot(data)
    plt.show()
 
	# 打印进程
	print("finishing",Path(file_path).name) 
 

# 定义错误回调函数，如果发生错误，能将错误在主进程中打印出来       
def error_callback(e):
    print('Error:', e)        

  # 将总数据集进行分割，分割成子数据集
def task_split(path,batch_num):
  pathlist = list(Path(path).glob("*"))
  # 数据集长度
  Len_path = len(pathlist)            
  param_dict ={}
  
  ## 数据分割
    
  if Len_path <= batch_num:
    batch_num = Len_path    
    
  for i in range(batch_num):
    task = "task" + str(i)
    param_dict[task] = [(pathlist[i])]
    
  if Len_path >= batch_num:
    for i in range(batch_num,Len_path):
      task = "task" + str(int(i%batch_num))
      param_dict[task].append(pathlist[i])
  
  return param_dict

if __name__ == '__main__':
    start_t = datetime.datetime.now()
    # 查看cpu核数
    num_cores = int(mp.cpu_count())
    print("本地计算机有: " + str(num_cores) + " 核心")
    # 开启核核数相等的进程数目
    # pool = mp.Pool(num_cores)    
    
    ## 遍历读取当前文件夹下所有子文件夹,并且转化为列表,我这里只开启8个进程
    batch_num = 8
    param_dict = task_split(Path.cwd(),batch_num)
    
    # 开启多进程处理数据
    pool = mp.Pool(batch_num)
    
    for name, param in param_dict.items():
      pool.apply_async(single_worker, args=(param,),error_callback=error_callback)
      
    # 关闭进程池
    pool.close()
    # 等待任务完成
    pool.join()

    end_t = datetime.datetime.now()
    elapsed_sec = (end_t - start_t).total_seconds()
    print("多进程计算 共消耗: " + "{:.2f}".format(elapsed_sec) + " 秒")

```

我处理10个结果，如果使用单进程需要20左右，开启8个进程，平均总时间能够减少到2s，多进程效果非常明显。



🌟注意：

必须把主进程写在「程序的入口」才能正常运行：

```python
if __name__ =='__main__':
    ....
```

这么做，能够避免重复执行主进程



# 参考链接：

[https://zhuanlan.zhihu.com/p/208996070](https://zhuanlan.zhihu.com/p/208996070)

[https://zhuanlan.zhihu.com/p/103135242](https://zhuanlan.zhihu.com/p/103135242)

[https://www.cnblogs.com/SkyOceanchen/p/11537587.html](https://www.cnblogs.com/SkyOceanchen/p/11537587.html)

https://zhuanlan.zhihu.com/p/340965963

