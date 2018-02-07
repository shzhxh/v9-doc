###概述

- PRM：实际上是一个Debian base system，安装在SD卡上。zedboard加电后首先会加载一个引导程序，引导程完成加载PRM和将RISCV CPU烧到FPGA中，PRM再引导Linux在RISCV CPU上启动。
- riscv-linux：运行在RISCV上的Linux。
- zedboard project：RISCV的vivado工程。

###安装过程

0. 运行平台准备

      - ubuntu 16.04 x86_64

      - zedboard开发板

      - 如下软件包是必需的，可以提前安装好

        ```shell 
        sudo apt install libncurses5-dev libncursesw5-dev qemu-user-static minicom libssl-dev
        ```
      - 需要安装vivado2017.4
        1. 从xilinx官网下载vivado2017.4(注：2017.3不能正常加载riscv的项目文件，请务必使用2017.4版本)
        2. 解压
        3. 运行xsetup
        4. 安装在/opt/Xilinx目录下
        5. Linux下安装完后需要多一步，安装cable驱动
        ```bash
          cd Xilinx/Vivado/2017.4/data/xicom/cable_drivers/lin64/install_script/install_drivers/
         sudo ./install_drivers
        ```

        ​

1. 准备运行环境
   ```bash
   # 下载labeled-riscv源码
   git clone https://github.com/LvNA-system/labeled-RISC-V.git	# 下载源码
   cd labeled-RISC-V
   git submodule update --init	# 安装子模块

   # 编译riscv-tools
   cd riscv-tools	#在riscv-tools目录下
   git submodule update --init --recursive
   export RISCV=/opt/riscv/tool
   ./build.sh	# 如果编译32位版本，应运行build-rv32ima.sh脚本
   # 此时进到/opt/riscv/tools/bin目录下，看到的文件是riscv64-unknown-elf-*，而我们还需要riscv64-unknown-linux-gun-*，所以需要下面的步骤
   cd riscv-gun-toolchain
   ./configure --prefix=$RISCV	#生成makefile文件，用以生成riscv64-unknown-linux-gnu-*
   make linux	#编译，成功后会看到/opt/riscv/tools/bin目录下生成了我们需要的文件
   ```
2. 编译riscv-pk和riscv-linux
   ```shell
   cd ../fpga		#在fpga目录下
   mkdir build
   make -j8 sw
   #注编译不成功！应依据RISCV的值修改riscv-linux/arch/riscv/rootfs/initramfs.txt。再重新编译。
   #注若出现错误提示fatal error: limits.h: No such file or directory，这需要到sw/riscv-linux/arch/riscv/rootfs/repo/busybox目录下运行如下命令
   make menuconfig	# 修改Busybox Settings->Build Options下sysroot的值
   # 如果提示一些so文件找不到，应把sysroot/lib下的文件复制到lib目录下，这个方法只是权宜之计！！！
   ```

3. 模拟器中运行

   ```bash
   # 编译最小化rootfs
   cd ../../sw/riscv-linux	#进入riscv-linux目录
   make ARCH=riscv menuconfig	# 将`General setup` -> `Initramfs source file(s)` 修改为 `arch/riscv/rootfs/initramfs-emu.txt`
   cd ../../labeled-RISC-V/fpga
   make -j8 sw

   # 编译和运行模拟器
   cd fpga/emulator
   make -j8 run-emu	# 未成功
   ```

4. FPGA中运行

  - 编译vivado项目(在fpga目录)

     ````
     source /opt/Xilinx/Vivado/2017.4/settings64.sh
     source /opt/Xilinx/SDK/2017.4/settings64.sh
     make project PRJ=myproject BOARD=zedboard
     ````

     1. 在vivado中打开此项目,即build/myroject-zedboard/myprojct-zedboard.xpr
     2. 生成bitstream，即Synthesis->Implementation->Bitstream


  - 准备SD卡

    1. 编译fsbl.elf(First Step Boot Loader)

       - Vivado->file->export->Export Hardware，在build/myproject-zedboard/myproject-zedboard.sdk目录下会生成一个叫system_top.hdf的文件。

         注：需要把gmake 链接为make

       - 运行hsi命令，hsi是vivado SDK附带的命令

         ```shell
         cd board/zedboard/boot
         hsi -nojournal -nolog -source gen-fsbl.tcl -tclargs ../../../build/myproject-zedboard/myproject-zedboard.sdk/system_top.hdf
         # 生成的文件在build目录下，叫excutable.elf，应把excutable.elf重命名为fsbl.elf
         ```

         ​

    2. 编译u-boot

       ```shell
       git clone https://github.com/xilinx/u-boot-xlnx
       cd u-boot-xlnx
       make zynq_zed_defconfig
       make CROSS_COMPILE=arm-linux-gnueabihf-
       cp u-boot u-boot.elf
       ```

       ​

    3. 编译boot.bin

       - 将fsbl.elf和u-boot.elf和system_top.bit文件放在build目录下
       - 修改的不是gen-bootbin.sh，而是bootgen.bif文件，添加以上文件正确的路径名。且system_top.bit应在u-boot.elf的前面，否则后继步骤里PRM无法正常启动。
       - `bash gen-bootbin.sh   #BOOT.BIN将会生成在build目录`

    4. 编译linux kernel

       ```powershell
       git clone https://github.com/xilinx/linux-xlnx
       cd linux-xlnx
       git checkout f1b1e077d641fc83b54c1b8f168cbb58044fbd4e  # 到2017.03的版本
       make ARCH=arm xilinx_zynq_defconfig	# 可以在linux-xlnx/arch/arm/configs/目录找到
       make ARCH=arm menuconfig
       #  General setup -> Cross-compiler tool prefix: `arm-linux-gnueabihf-`
       #  General setup -> 不选 `Initial RAM filesystem and RAM disk (initramfs/initrd) support`
       # device drivers -> character devices -> serial drivers -> 选上xilinx uartlite serial port support
       make ARCH=arm -j8
       # 可以看到在arch/arm/boot下生成了zImage
       ```

       ​

    5. 编译system.dtb

       注：目前(2018.02.07)system.dtb尙有问题，请暂时从网上下载[system.dtb](https://github.com/shzhxh/v9-doc/blob/master/LabeledKernel/system.dtb)。

       ```shell
       git clone https://github.com/xilinx/device-tree-xlnx
       cd labeled-RISC-V/fpga/board/zedboard/boot
       vim gen-dts.tcl # 将device_tree_repo_path修改为上方刚clone的仓库
        hsi -nojournal -nolog -source gen-dts.tcl -tclargs ../../../build/myproject-zedboard/myproject-zedboard.sdk/system_top.hdf
        cd build/dts
        vim system-top.dts	# 插入/include/ "system.dts"
        #将如下内容插入到system-top.dts文件中
        / {
         chosen {
           bootargs = "root=/dev/mmcblk0p2 rootfstype=ext4 rootwait earlycon clk_ignore_unused";
         };
       };
        / {
         reserved-memory {
           #address-cells = <1>;
           #size-cells = <1>;
           ranges;

           mem_reserved: buffer@100000000 {
             reg = <0x10000000 0x10000000>;
           };
       	};
       };

       # 最后，运行如下命令
       dtc -I dts -O dtb -o system.dtb system-top.dts
       ```

       ​

    6. 在SD卡上安装PRM

       - 将SD卡接到电脑

       - cfdisk命令格式化SD卡，创建一个50M分区，一个2G分区

       - mkfs命令将50M分区格式化为vfat，2G分区格式化为ext4

       - 将2G分区挂载到/mnt, 将50M分区挂载到/mnt/boot

       - 将上几步生成的BOOT.BIN，system.dtb， zImage复制到/mnt/boot目录下

       - 安装debian基本系统

         ```shell
         sudo qemu-debootstrap --arch armel stable /mnt http://ftp.debian.org/debian
         sudo chroot /mnt /bin/bash	# chroot到SD卡的基本系统
         passwd	# 设置密码
         apt-get update
         apt-get install openssh-server vim build-essential minicom tmux localehelper inetutils-tools net-tools
         exit
         vim /mnt/etc/securetty # 添加ttyPS0
         vim /mnt/etc/ssh/sshd_config  # 添加PermitRootLogin yes
         vim /mnt/etc/fstab #添加如下内容,不要加上左边的大于号
         > # <file system> <mount point> <type>  <options> <dump>  <pass>
         > proc /proc proc defaults 0 0
         > /dev/mmcblk0p1 /boot vfat defaults 0 2
         > /dev/mmcblk0p2 / ext4 errors=remount-ro 0 1

         ```

         ​

  - 设置开发板为SD boot模式

  - 启动PRM Linux，PRM就是安装在SD卡上的debian基本系统

    1. 接上zedboard，用uart连到电脑，上电。

    2. minicom -D /dev/ttyACM0

    3. 运行如下命令

       ```
       load mmc 0:auto 0x3000000 zImage
       load mmc 0:auto 0x2a00000 system.dtb
       bootz 0x3000000 - 0x2a00000
       ```

       ​

  - 启动RISC-V子系统

    1. 将board/zedboard/prm-loader/loader.c和build/linux.bin复制到SD卡中。

    2. 编译loader

       ```shell
       gcc -o loader loader.c
       ```

    3. 通过ssh连到zedboard，用tmux开启两个窗格

       ```shell
       minicom -D /dev/ttyUL1	# 一个窗格中运行此命令
       ./loader linux.bin configstr	# 另一个窗格中运行此命令
       ```

       注：启动时间大概在2分钟左右


