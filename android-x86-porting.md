#### Porting实验
- - 第一步 代码同步

  ```shell
  mkdir oreo-x86
  cd oreo-x86
  repo init -u git://192.168.0.185/android-x86/manifest.git -b multiwindow-oreo
  repo sync
  ```

  > repo的作用：是获取android源代码的工具，是通过调用git命令来实现的。其实是个python脚本。
  >
  > repo的安装：sudo apt install repo
  >
  > repo的用法：repo \[command] \[options] 
  >
  > repo的init命令：在当前目录下初始化repo，-u URL是指定仓库的位置，-b REVISION是指定分支或版本。
  >
  > repo的sync命令：将工作树更新到最新的版本，我感觉就是把远程代码下载到本地
  >
  > 注意：在使用以上命令的时候，会出现一个fatal error，提示无法连接到googlesource.com。可以通过修改REPO_URL来解决以上问题。代码如下：

  ```shell
  sudo vim /usr/bin/repo
  # 将REPO_URL行替换为如下内容
  REPO_URL = 'https://gerrit-google.tuna.tsinghua.edu.cn/git-repo'
  ```

- 第二步 编译

  `repo start multiwindow-oreo --all`

  > repo的start命令：创建一个新的分支， --all是在所有的项目中都创建分支

  ```shell
  source build/envsetup.sh
  lunch openthos_emu_x86_64-eng
  make iso_img -j$ProcessorNum
  ```

  > 考虑到编译过程太过漫长，未在本机编译。
  >
#### android-x86培训总结与思考

- 操作系统供应商应该是乐于见到自己的系统支持广泛的架构的，android应该是Linux内核加了一层壳，Linux内核已经支持广泛的架构了，为什么android反而只能支持arm呢？以至于需要android-x86这样的开源组织花费大量的精力才能使android支持x86架构呢？

  本质上是硬件厂商为了保护自己的利益，而没有将自己的硬件细节公布出来。所以，它们在Linux内核外面加了一个HAL层，在Linux内核里加了一些不能独立运行的驱动，使HAL层和驱动配合才能完成独立的功能。这样就人为地增加了移植到其它架构的困难。

- 内核移植的过程是怎样的？

  1. 下载代码，观察到下载后的代码顶层是有22个目录，如果我没理解错的话，应该是460多个项目的集合。
  2. 配置设备，应该主要是和device目录下的方件相关
  3. 增加必须的补丁，对此不是很懂，不太清楚这些补丁是要打到哪里去的
  4. 编译并修复编译中的错误，既然这步已经开始尝试编译了，我推测2、3步主要是针对驱动和HAL层的。
  5. 编译好操作系统后，尝试启动，并修复启动过程中的问题。

  综上，内核移植看上去似乎主要是在修改配置文件和Makefile文件。

- android-x86启动过程是怎样的？

  1. 在标准android启动之前，先启动了一个initrd.img，这应该是个精简的Linux系统，Mr. 黄的思路应该是用linux来引导android系统。
  2. 标准android的init进程，主要在system/core/init目录。
  3. 启动ueventd进程，主要在system/core/init目录，它与busybox很像。**在启动过程中最多的修改就在此处**.
  4. 初始化各个HALs，通过init.sh实现，但我尚不知道init.sh的位置。
  5. zygote --> system_server --> HAL section --> HIDL HAL，以上是接下来的启动步骤，我目前并不能对应到代码的具体位置。

- 图形处理的原理是怎么样的？

  drn_gralloc --> LibGLES_mesa --> libdrm --> KMS driver --> GPU

  以上看起来是个宏观的从顶层到底层的调用过程，我需要进一步观察具体的过程。

- 什么是HAL？

  HAL包括3方面的内容：库，HAL和服务。应该是每一个设备都对应一个HAL。

- 什么是vold?

  应该是vold目录内的东西，但我并不知道vold本身是什么意思。

  1. device/generic/common/fstab.x86，主要讲解了分区挂载。
  2. 电源管理，主要讲解了挂起的原理。
  3. houdini，是用来模拟arm架构的。
  4. GMS，不清楚是什么。
  5. 其他。

- 什么是firmware?

  可以把每个硬件理解为一个处理器，firmware就是各个处理器的指令集。