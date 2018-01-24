### 什么是silo？

TailBench里的一个应用程序，是一个内存里的OLTP数据库。

Harness控制着程序的运行，并测量所需的延迟。

每个TailBench的应用程序由服务组件和客户组件两部分组成。

### 从哪里获取？

MIT官网。

### 环境变量

- TBENCH_WARMUPREQS:一个warmup时间段，在此时间内不测量延迟。
- TBENCH_MAXREQS:测量期间需要执行的请求数，不包含warmup请求。
- TBENCH_MINSLEEPNS:在idle时间段客户程序沉睡的最小时间段(以ns为单位)。
- TBENCH_QPS:测量期间的平均请求数(每秒查询数)，harness使用指数分布生成间隔时间。
- TBENCH_RANDSEED:随机数生成器的种子。
- TBENCH_CLIENT_THREADS:客户线程生成请求的数量。
- TBENCH_SERVER:server的URL或IP地址，默认为localhost。
- TBENCH_SERVER_PORT:server使用的TCP/IP端口，默认为8080。

### 需要提前安装的软件包

注：也许有些不是必须的。

```
sudo apt install uuid-dev libssl-dev libaio-dev google-perftools libboost-all-dev libboost-dev libboost-test-dev openjdk-8-jdk libjemalloc-dev libnuma-dev libpthread-stubs0-dev libxapian-dev libreadline-dev libdb5.3-dev libdb5.3++-dev libgtop2-dev libopencv-dev autoconf libncurses-dev ant libtool swig bison libbz2-dev git autoconf libgtop2-dev libreadline6 libreadline6-dev libncurses-dev libjemalloc-dev libdb++-dev libaio1 libssl-dev libcrypto++-dev python-dev swig python-numpy python-scipy
```
### build silo

- 在build或运行程序之前，应将configs.sh里的参数设置为合适的值，其实就是设置好文件目录.

- 如果要使用secjbb，应将Makefile.config里的参数设置好，其实就是java所在的目录。

- 编辑silo/Makefile，删除Werror选项，否则无法编译通过。

- 以上事项做好后，运行如下命令：

  `./build.sh silo`
### 运行silo
```
cd silo
sudo ./run.sh	#运行结果是二进制文件，保存在silo/lats.bin
```

### 结果分析

在tailbench-v0.9目录使用如下命令分析lats.bin

`python utilities/parselats.py silo/lats.bin`