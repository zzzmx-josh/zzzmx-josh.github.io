---
title: mpirun运行参数
date: 2023-04-20 22:16:19
tags: mpirun
---

对于mpirun运行参数存在疑问，[转载来源](https://www.cnblogs.com/devilmaycry812839668/p/15132333.html)

相关资料，参看前文：

[https://www.cnblogs.com/devilmaycry812839668/p/15107935.html](https://www.cnblogs.com/devilmaycry812839668/p/15107935.html)

现有硬件：两台装有Ubuntu18.04的操作系统（下面简称A电脑，B电脑）

A电脑： 24物理核心（48逻辑核心）

B电脑：6物理核心（12逻辑核心）

网络：

A、B电脑之间使用100M以太网交换机连接（就是TP-Link路由器）。

其中，A电脑IP为 192.168.11.66， B电脑IP为 192.168.11.206

本文中的代码   x.py  :

```python
from mpi4py import MPI
import numpy as np
 
comm = MPI.COMM_WORLD
size = comm.Get_size()
rank = comm.Get_rank()
 
sendbuf = np.zeros(100*10000, dtype='i') + rank
recvbuf = None
if rank == 0:
    recvbuf = np.empty([size, 100*10000], dtype='i')
 
print( MPI.Get_processor_name() )
 
import time
a = time.time()
for _ in range(1):
    comm.Gather(sendbuf, recvbuf, root=0)
b = time.time()
if rank == 0:
    print(b-a)
```

本文所有的命令均为在主机A上执行，所以本文中对myhosts文件的编写都是在A主机下进行的。

# 参数   --machinefile

该参数主要是用在分布式环境下，在单机环境该参数没有意义。该参数就是指定分布式环境下有几台主机，并且可以指定每台主机最多可以开几个CPU进行计算。

具体命令:

`mpirun -np 8  --machinefile myhosts   /home/xxx/anaconda3/bin/python x.py`

其中， myhosts 为我们需要编写的文本文件，该文件指定mpi分布式环境下各个主机的IP及可以运行的最多CPU数。

myhosts文件最基本的设置就是不指定每个主机最多可以运行的CPU数，那么此时每台主机最多可以运行的CPU数为多少呢，这时每台主机最多可以运行的CPU数为该主机的物理CPU核心数，本文中主机A 192.168.11.66的最多可以运行CPU数为24， 主机B 192.168.11.206最多可以运行的CPU数为6。

最基本的 myhosts ：

```shell
cat myhosts
192.168.11.66       
192.168.11.206 
```

myhosts中给出分布式环境下两个主机IP，此时每个主机最多可以使用的CPU数为物理核心个数。

执行命令：

`mpirun -np 8  --machinefile myhosts   /home/xxxxxx/anaconda3/bin/python x.py`



运行结果显示8个进程均运行在主机A上 （因为运行命令本身就是在主机A上运行的，==所有优先使用主机A的计算资源==）。

执行命令：

\`mpirun -np 24 --machinefile myhosts /home/xxxxxx/anaconda3/bin/python x.py\`

运行结果显示24个进程均运行在主机A上 （因为运行命令本身就是在主机A上运行的，所有优先使用主机A的计算资源）。

执行命令：

`mpirun -np 25  --machinefile myhosts   /home/xxxxxx/anaconda3/bin/python x.py`

运行结果显示共运行25个进程，其中24个进程运行在主机A上， 一个进程运行在主机B上 （因为运行命令本身就是在主机A上运行的，所有优先使用主机A的计算资源）。

因为主机A最多可以利用的CPU个数为24，所以需要有一个进程运行在主机B上。

执行命令：

`mpirun -np 30  --machinefile myhosts   /home/xxxxxx/anaconda3/bin/python x.py`

运行结果显示共运行30个进程，其中24个进程运行在主机A上， 6个进程运行在主机B上 （因为运行命令本身就是在主机A上运行的，所有优先使用主机A的计算资源）。

因为主机A最多可以利用的CPU个数为24，所以需要有6个进程运行在主机B上。

执行命令：

`mpirun -np 31  --machinefile myhosts   /home/xxxxxx/anaconda3/bin/python x.py`

运行结果报错，显示信息：

```python
-------------------------------------------------------------------------
There are notenough slots available inthe system to satisfy the 31
slots that were requested by the application:
/home/xxxxxx/anaconda3/bin/python
Either request fewer slots foryour application, or make more slots
available for use.
A "slot"isthe Open MPI term for an allocatable unit where we can
launch a process.  The number of slots available are defined by the
environment in which Open MPI processes are run:
1. Hostfile, via "slots=N" clauses (N defaults to number of
     processor cores ifnot provided)
  2. The --host command line parameter, via a ":N" suffix on the
     hostname (N defaults to 1 ifnot provided)
  3. Resource manager (e.g., SLURM, PBS/Torque, LSF, etc.)
  4. If none of a hostfile, the --host command line parameter, or an
     RM is present, Open MPI defaults to the number of processor cores
In all the above cases, if you want Open MPI to default to the number
of hardware threads instead of the number of processor cores, use the
--use-hwthread-cpus option.
Alternatively, you can use the --oversubscribe option to ignore the
number of available slots when deciding the number of processes to
launch.
--------------------------------------------------------------------------
```

报错信息显示可以运行的CPU个数不够，因为A主机最多运行24个CPU，B主机最多运行6个CPU，所以当前系统下最多可以运行的CPU个数为30，超出这个个数则会报错。

# 参数  slots

进阶版的myhosts的编写，指定每个主机最多可以使用的CPU个数，这个CPU个数最好是小于指定主机的物理核心数，否则该设定没有意义：

```
cat myhosts
192.168.11.66       slots=4
192.168.11.206      slots=4
```

指定主机A 、B中每个主机最多可以使用cpu个数均为4，其中每个主机IP（或主机名）后面的的slots的数值可以自由设定，不过只能小于等于该主机的物理核心数。

执行命令：

`mpirun -np 4  --machinefile myhosts   /home/xxxxxx/anaconda3/bin/python x.py`

运行结果显示4个进程全部运行在主机A上。

执行命令：

`mpirun -np 6  --machinefile myhosts   /home/xxxxxx/anaconda3/bin/python x.py`

运行结果显示4个进程全部运行在主机A上，2个进程运行在主机B上。

执行命令：

`mpirun -np 8  --machinefile myhosts   /home/xxxxxx/anaconda3/bin/python x.py`

运行结果显示4个进程全部运行在主机A上，4个进程运行在主机B上。

执行命令：

`mpirun -np 9  --machinefile myhosts   /home/xxxxxx/anaconda3/bin/python x.py`

运行结果报错，显示信息：

```shell
--------------------------------------------------------------------------
There are not enough slots available in the system to satisfy the 31
slots that were requested by the application:

  /home/xxxxxx/anaconda3/bin/python

Either request fewer slots for your application, or make more slots
available for use.

A "slot" is the Open MPI term for an allocatable unit where we can
launch a process.  The number of slots available are defined by the
environment in which Open MPI processes are run:

  1. Hostfile, via "slots=N" clauses (N defaults to number of
     processor cores if not provided)
  2. The --host command line parameter, via a ":N" suffix on the
     hostname (N defaults to 1 if not provided)
  3. Resource manager (e.g., SLURM, PBS/Torque, LSF, etc.)
  4. If none of a hostfile, the --host command line parameter, or an
     RM is present, Open MPI defaults to the number of processor cores

In all the above cases, if you want Open MPI to default to the number
of hardware threads instead of the number of processor cores, use the
--use-hwthread-cpus option.

Alternatively, you can use the --oversubscribe option to ignore the
number of available slots when deciding the number of processes to
launch.
--------------------------------------------------------------------------
```

报错信息显示可以运行的CPU个数不够，因为A主机我们指定最多运行4个CPU，B主机最多运行4个CPU，所以当前系统下最多可以运行的CPU个数为8，超出这个个数则会报错。

# 参数 -np ：

如果我们运行时不使用 -np 参数， 那么运行情节如何呢：

在  myhosts 文件内容：

```shell
192.168.11.66
192.168.11.206
```

运行命令:

\`mpirun --machinefile myhosts /home/xxxxxx/anaconda3/bin/python x.py\`

运行结果，A主机运行24个进程，B主机运行6个进程，也就是说不指定 -np参数 ==每个主机都是以全部的物理核心来运行进程。==

如果在  myhosts 文件内容：

```shell
192.168.11.66       slots=4
192.168.11.206      slots=4
```

运行命令:

`mpirun --machinefile myhosts   /home/xxxxxx/anaconda3/bin/python x.py`

运行结果，A主机运行4个进程，B主机运行4个进程，也就是说不指定 -np参数 和之前一样每个主机都是以全部的可以运行的CPU个数来运行进程。（因为这里在myhosts文件中使用了slots参数已经设定了A主机最多可以使用4个CPU，B主机最多可以使用4个CPU）

# 参数  -nolocal

在执行mpi命令时加入参数 -nolocal 则指定不运行当前所在主机上的CPU，具体：

假设myhosts文件内容如下：

```shell
cat myhosts
192.168.11.66       slots=4
192.168.11.206      slots=4
```

myhosts 文件指定A、B主机均只能最多使用4个CPU。

在主机A 192.168.11.66 上运行命令：

\`mpirun -nolocal --machinefile myhosts /home/xxxxxx/anaconda3/bin/python x.py\`

报错：

```shell
--------------------------------------------------------------------------
There are notenough slots available inthe system to satisfy the 8
slots that were requested by the application:
/home/xxxxxx/anaconda3/bin/python
Either request fewer slots foryour application, or make more slots
available for use.
A "slot"isthe Open MPI term for an allocatable unit where we can
launch a process.  The number of slots available are defined by the
environment in which Open MPI processes are run:
1. Hostfile, via "slots=N" clauses (N defaults to number of
     processor cores ifnot provided)
  2. The --host command line parameter, via a ":N" suffix on the
     hostname (N defaults to 1 ifnot provided)
  3. Resource manager (e.g., SLURM, PBS/Torque, LSF, etc.)
  4. If none of a hostfile, the --host command line parameter, or an
     RM is present, Open MPI defaults to the number of processor cores
In all the above cases, if you want Open MPI to default to the number
of hardware threads instead of the number of processor cores, use the
--use-hwthread-cpus option.
Alternatively, you can use the --oversubscribe option to ignore the
number of available slots when deciding the number of processes to
launch.
--------------------------------------------------------------------------
```

也就是说 -nolocal 不允许本地主机A参与计算，而 myhosts文件中又允许A主机参与计算，因此造成冲突。在没有使用 -np 参数的情况下是需要使用myhosts文件中指定的CPU数的最大值来运行的，但是-nolocal不允许A主机参与运行无法满足myhosts文件中的8个CPU的设定，因此报错。



我们在上面的运行语句中改进下，如下：

` mpirun -np 6  -nolocal  --machinefile myhosts   /home/xxxxxx/anaconda3/bin/python x.py`

依旧报错误：



`There are notenough slots available inthe system to satisfy the 6
slots that were requested by the application:`

原因是不使用本地主机A的情况下 -np 指定需要6个CPU运行，但是myhosts中指定B主机192.168.11.206最多可以运行4个CPU，因此不满足6个CPU运行的要求报错。

我们在上面的运行语句中改进下，如下：

` mpirun -np 4  -nolocal  --machinefile myhosts   /home/xxxxxx/anaconda3/bin/python x.py`

成功运行。四个进程全部运行在B主机192.168.11.206 上。既满足 -np 4 也满足 -nolocal 设定，同时也满足 myhosts中的设定。



同理：

上面的运行语句中改进下，如下：

` mpirun -np 2  -nolocal  --machinefile myhosts   /home/xxxxxx/anaconda3/bin/python x.py`

成功运行。2个进程全部运行在B主机192.168.11.206 上。既满足 -np 2 也满足 -nolocal 设定，同时也满足 myhosts中的设定。


# 参数  --use-hwthread-cpus  与  --oversubscribe

前面我们知道 A、B主机的CPU物理核心个数：

A电脑： 24物理核心（48逻辑核心）

B电脑：6物理核心（12逻辑核心）

==-np 指定该次运行一共需要的CPU个数==，-nolocal 指定不使用当前主机的CPU进行运算，myhosts中指定参与计算的各主机的最多参与计算的CPU个数。

正如我们前面所说的，myhosts文件中虽然可以指定每个主机最多可以使用的CPU个数，但是这个个数是我们人为设定的，设定的一个要求就是要小于主机的物理核心个数。如果myhosts 中slots指定的CPU数量等于主机物理核心个数那么slots本身是没有意义的，因为myhosts中不使用slots设定所能使用的最多CPU个数也是该主机的物理核心个数。

那么 myhosts 中slots的个数设定真的不能大于主机的物理核心数，其实不然。之所以我们默认要求slots个数不能大于物理核心数是因为在**独占**主机进行**计算密集型**运算时当主机上运行的进程数等于物理核心数时往往会得到最高的利用率。

一个隐藏知识，根据Intel cpu的白皮书（蓝皮书）可以看到在使用超流水线多线程运算时密集计算型计算性能可以提高30%，这就是说在Intel超流水线技术支持下，密集计算任务单主机下进程数等于逻辑核心个数其性能要==超进程数等于物理核心数==时的30%，不过这只是在短时间计算情景下，如果在长时间运行情况下当进程数等于逻辑核心数时计算密集型任务往往会导致CPU的散热撞到功率墙（散热墙）从而导致大幅度CPU降频，从而导致计算性能大幅下降，当然这说的是普通散热情况下，因此在进行计算密集型计算任务时我们都是默认设定进程数等于物理核心数。

也就是说，如果我们在 myhosts 文件中设定 slots 个数超过主机的物理核心数在不考虑计算性能的情况下是完全可行的。

给出此时的myhosts内容：

```shell
cat myhosts
192.168.11.66       slots=100
192.168.11.206      slots=100
```

运行语句中如下：

`mpirun -np 200  --machinefile myhosts   /home/xxxxxx/anaconda3/bin/python x.py`

成功运行。共运行200个进程，其中100个进程运行在A主机192.168.11.66 上， 100个进程运行在B主机192.168.11.206 上。

由此可见使用 myhosts文件中的slots设定也是可以运行超过物理核心数的进程的。

刚才说的是在使用 --machinefile 参数 利用myhosts 文件中的设定来实现超过物理核心数的进程数量运行的，如果我们不使用 --machinefile 参数的情况下呢？？？

# 参数 --host

执行命令：

`mpirun -np 8  --host 192.168.11.66:4 --host 192.168.11.206:4   /home/xxxxxx/anaconda3/bin/python x.py`

成功在A主机192.168.11.66主机上运行4个进程，在B主机192.168.11.206上运行4个进程，共运行8个进程。

执行命令：

`mpirun -np 6  --host 192.168.11.206:6   /home/xxxxxx/anaconda3/bin/python x.py`

成功在B主机192.168.11.206上运行6个进程，共运行6个进程。

执行命令：

`mpirun -np 200  --host 192.168.11.66:100 --host 192.168.11.206:100   /home/xxxxxx/anaconda3/bin/python x.py`

成功在A主机192.168.11.66主机上运行100个进程，在B主机192.168.11.206上运行100个进程，共运行200个进程。



执行命令：

`mpirun -np 200 --host 192.168.11.206:200   /home/xxxxxx/anaconda3/bin/python x.py`

成功在在B主机192.168.11.206上运行200个进程，共运行200个进程。

当然上面的都是在分布式的环境下运行的（ 分布式环境下是指使用 --host 参数）。

如果不使用 --host 参数，在单机环境下如何实现超过物理核心数的进程数运行呢？？？

如：

执行命令：

`mpirun -np 48   /home/xxxxxx/anaconda3/bin/python x.py`

命令的含义是在A主机192.168.11.66上运行48个进程，而A主机的物理核心数为24，因此报错。

There are notenough slots available inthe system to satisfy the 48
slots that were requested by the application:

这时改用命令：（加入参数 --use-hwthread-cpus ）

\--use-hwthread-cpus 参数的含义是允许当前主机运行的进程最大数为逻辑核心数而不是物理核心数。

`mpirun -np 48 --use-hwthread-cpus  /home/xxxxxx/anaconda3/bin/python x.py`

成功在在A主机192.168.11.66上运行48个进程，A主机为当前命令执行时所在的主机，其逻辑核心数为48。

改命令为：

`mpirun -np 49 --use-hwthread-cpus  /home/xxxxxx/anaconda3/bin/python x.py`

运行失败，因为 --use-hwthread-cpus 参数只能设定最多运行进程数为逻辑核心数，因此超过48后报错（A主机逻辑核心数为48）。

这时改用 参数 --oversubscribe ：

\--oversubscribe 参数的含义就是不对进程数设限制，也就是说进程数可以随便设置。

执行命令如下：

`mpirun -np 200  --oversubscribe  /home/xxxxxx/anaconda3/bin/python x.py`

成功在A主机192.168.11.66上运行了200个进程。



# 附加内容：

在执行mpi程序时rank0进程是在哪个主机上呢？？？

（rank0进程就是mpi程序运行后rank排名号为0号的进程）

在主机A 192.168.11.66 上：

myhosts文件内容：

```shell
192.168.11.66       slots=4
192.168.11.206      slots=4
```

执行命令：

`mpirun -np 8   --machinefile myhosts   /home/xxxxxx/anaconda3/bin/python x.py`

运行结果显示，rank0进程运行在A主机 192.168.11.66上。

同理：

在主机B 192.168.11.206 上：

myhosts文件内容：

```shell
192.168.11.66       slots=4
192.168.11.206      slots=4
```

执行命令：

`mpirun -np 8   --machinefile myhosts   /home/xxxxxx/anaconda3/bin/python x.py`

运行结果显示，rank0进程运行在B主机 192.168.11.206上。

由上面的运行情况我们可以知道 rank0 进程一般都是运行在启动mpi程序并使用CPU运行进程的主机上（需要排除使用参数 -nolocal 的情况，该种情况启动mpi程序的主机是不使用CPU参与计算的，因此rank0进程此时是不在启动mpi程序的主机上的）

