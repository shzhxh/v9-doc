1. 准备运行环境
   ```bash
   # 下载labeled-riscv源码
   git clone https://github.com/LvNA-system/labeled-RISC-V.git	# 下载源码
   cd labeled-RISC-V
   git submodule update --init	# 安装子模块

   # 编译riscv-tools
   cd riscv-tools	#在riscv-tools目录下
   export RISCV=/opt/riscv/tool
   git submodule update --init --recursive
   ./build.sh	# 如果编译32位版本，应运行build-rv32ima.sh脚本

   # 编译riscv-pk和riscv-linux
   cd ../fpga		#在fpga目录下
   mkdir build
   make -j16 sw	#注编译不成功！依据RISCV的值修改riscv-linux/arch/riscv/rootfs/initramfs.txt依然不成功！
   ```

2. 模拟器中运行

   ```bash
   # 编译最小化rootfs
   cd ../../sw/riscv-linux	#进入riscv-linux目录
   make ARCH=riscv menuconfig	#错误！

   # 编译和运行模拟器
   cd emulator
   make -j16 run-emu
   ```

3. FPGA中运行

   ```bash
   # 需要安装vivado2017.4

   # 编译vivado项目(在fpga目录)
   make project PRJ=myproject BOARD=zedboard
   # 在vivado中打开此项目,即build/myroject-zedboard/myprojct-zedboard.xpr
   # 生成bitstream，即Synthesis->Implementation->Bitstream

   # 准备SD卡
   # 设置开发板为SD boot模式
   # 启动Linux，问题：PRM是从哪步得到的？
   # 启动RISC-V子系统

   ```

