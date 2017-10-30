### 操作系统接口
- 先说结论：xv6本身是对unix v6的再实现版，只有21个系统调用接口。而v9上的xv6除了以上接口，还多了7个接口lseek,socket,bind,listen,poll,accept,connect。另外，v9上的xv6未见到shell的实现。
- 对内存和CPU的抽象
  1. fork: 创建进程
  2. exit: 结束当前进程
  3. wait: 等待子进程结束
  4. kill(pid): 结束pid所指的进程
  5. getpid(): 获取当前进程的pid
  6. sleep(n): 睡眠n秒
  7. exec: 加载并执行一个文件
  8. sbrk(n): 为进程内存空间增加n字节**此函数实现似乎不一致**
- 对硬盘的抽象
  1. open(filename, flags): 打开文件
  2. read(fd, buf, n): 文件的内容读到内存中**增加了功能**
  3. write(fd, buf, n): 内存中的内容写入到文件中**此函数实现似乎不一致**
  4. close(fd): 关闭文件**即为sys_close**
  5. dup(fd): 复制fd**dup2看起来sys_dup不太一样**
  6. pipe(p): 创建管道**和sys_pipe看起来不太一样**
  7. chdir: 改变目录**和sys_chdir基本相同**
  8. mkdir: 创建目录**sys_mkdir**
  9. mknod: 创建设备文件**sys_mknod**
  10. fstat: 返回文件信息**和filestat类似但多了一些功能**
  11. link(f1, f2): 给f1创建一个新名字f2**即为sys_link**
  12. unlink: 删除文件**sys_unlink**

### 第一个进程
**struct proc基本相同，v9上的xv6多了next和pre**  
- 第一个地址空间  
  **没有bootloader，内核被v9计算机自动装载入内存**  
  **没有物理地址到高地址的映射，直接进行的页表映射**  
  **请仔细阅读内存初始化部分是否相同**
- 创建第一个进程  
  **mainc函数少了很多初始化的工作**  
  **userinit**  
  **allocproc代码略微不同，但功能完全一样**
- 运行第一个进程
- 第一个系统调用

### 页表
- 分页机制
- 进程地址空间
- 物理内存的分配
- 地址空间中的用户部分

### 陷入、中断和驱动程序

### 锁

### 调度

### 文件系统
