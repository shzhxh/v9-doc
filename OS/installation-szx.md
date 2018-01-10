**The role of this document is to record the process of installation**

**ON Ubuntu 16.04 x86_64**

1. Clone bbl-ucore to local folder

   `git clone https://github.com/ring00/bbl-ucore.git`

2. Install toolchain for RV32

   Note: I also test the official repository https://github.com/riscv/riscv-tools, it works well.

   ```shell
   # It is better to clone this repertory to a different folder.
   $ git clone https://github.com/ring00/riscv-tools.git
   # autoconf, automake and autotools-dev : tools to form Makefile
   # curl : a tool to transfer data from or to a server
   # libmpc-dev : multiple precision complex floating-point library development package
   # libmpfr-dev : multiple precision floating-point computation developers tools
   # libgmp-dev : Multiprecision arithmetic library developers tools
   # gawk : a tool to find and replace text
   # build-essential : informational list of packages which are considered essential for building packages.
   # bison, flex : tools to create a compiler.
   # texinfo : a documentation system that uses a single source file to produce both on-line information and printed output
   # gperf : generate a perfect hash function from a key set
   # libtool : Generic library support script
   # patchutils : Utilities to work with patches
   # bc : GNU bc arbitrary precision calculator language
   # zlib1g-dev : compression library for development
   $ sudo apt install autoconf automake autotools-dev curl libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev
   # When the project with git clone comes with the submodule, the contents of the submodule are not automatically downloaded at the beginning, so it needs the following command to download the essential submodule.
   $ git submodule update --init --recursive
   # Define the location of object file, e.g. /opt/riscv/toolchain
   $ export RISCV=/path/to/install/riscv/toolchain
   # Build the rv32g toolchain
   $ ./build-rv32g.sh
   ```

   If it mentions error like this：

   > configure: error: Building GCC requires GMP 4.2+, MPFR 2.4.0+ and MPC 0.8.0+.

   Then GMP, MPFR and MPC should be installed:

   ```shell
   # Download the latest version from ftp://gcc.gnu.org/pub/gcc/infrastructure
   # When I download, it is mpc-1.0.3, mpfr-3.1.4, gmp-6.1.0
   # 1, gmp MUST be first to build
   cd gmp-6.1.0
   ./configure --prefix=/opt/gmp-6.1.0
   make
   sudo make install
   make check
   # 2, mpfr MUST be the second to build
   cd mpfr-3.1.4
   ./configure --prefix=/opt/mpfr-3.1.4 --with-gmp=/opt/gmp-6.1.0
   make
   sudo make install
   # 3, mpc MUST be the last to build
   cd mpc-1.0.3
   ./configure --prefix=/opt/mpc-1.0.3 --with-gmp=/opt/gmp-6.1.0 --with-mpfr=/opt/mpfr-3.1.4
   make
   sudo make install
   # 4, after that, we should modify build-rv32g.sh to tell the location of gmp,mpfr,mpc
   build_project riscv-gnu-toolchain --prefix=$RISCV --with-arch=rv32g --with-abi=ilp32d --with-gmp=/opt/gmp-6.1.0 --with-mpc=/opt/mpc-1.0.3 --with-mpfr=/opt/mpfr-3.1.4 --enable-linux
   ```

   Then run `./build-rv32g.sh` again.

3. Building bbl-ucore

   ```shell
   git submodule update --init --recursive
   cd labcodes
   ./gccbuildall.sh
   ```

   If it mentions error like this:

   > riscv32-unknown-linux-gnu-gcc: not found

   Then we should add riscv32-unknown-linux-gun-gcc to PATH:

   ```shell
   #open /etc/profile as a super user, add following text
   export PATH=$PATH:/opt/riscv/toolchain/bin
   #then close /etc/profile
   source /etc/profile
   # run gccbuildall.sh again
   ./gccbuildall.sh
   ```

4. Run Ucore on qemu

   It mentions error like this:

   > qemu-system-riscv32: Command not found

   I think it should to install riscv-qemu

   ```shell
   git clone https://github.com/riscv/riscv-qemu.git
   sudo apt install gcc libc6-dev pkg-config bridge-utils uml-utilities zlib1g-dev libglib2.0-dev autoconf automake libtool libsdl1.2-dev
   cd riscv-qemu
   git submodule update --init dtc
   git submodule update --init pixman
   ./configure --target-list=riscv64-softmmu,riscv32-softmmu --prefix=/opt/riscv/qemu
   make
   sudo make install
   # open /etc/profile as a super user, add qemu-system-riscv32 to $PATH
   export PATH=$PATH:/opt/riscv/qemu/bin
   # run qemu again
   make qemu
   ```

   It works, the installation complete.

   ​