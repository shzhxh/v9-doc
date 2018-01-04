[linux-kernel](https://www.ibm.com/developerworks/cn/linux/l-linux-kernel/)
- 分层结构

  - user space

    1. 应用程序
    2. glibc

  - kernel space

    1. 系统调用接口SCI(read,write...)

    2. 平台无关层

       PM

       VFS(fs目录)：是SCI和具体文件系统的交换层

       MM(mm目录)

       Network Stack(net目录):遵循网络协议本身的分层结构，其中socket层是标准API

    3. 平台相关层BSP

       Arch(arch目录)

       DD(drivers目录)
