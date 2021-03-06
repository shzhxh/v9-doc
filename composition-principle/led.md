#### 实验内容：控制开发板上LED灯闪烁

#### 实验目的：了解FPGA的开发流程

#### 实验原理：

1. 四个LED灯连接在FPGA芯片的四个管脚上，通过控制管脚控制LED的闪烁
2. 通过从时钟获取的时间来控制LED灯闪烁的频率

#### 实验过程：

1. 打开vivado
2. 创建新工程。RTL project意为逻辑设计，看上去是自由度最大的设计模式。IP核是第三方提供好的模块，方便快速开发。要注意选择与开发板对应的芯片编号。
3. 添加设计文件。定义一个寄存器用来计时，在固定时间翻转LED灯的值。
4. 添加约束文件。约束电压和管脚号。
5. 综合。点击Run Synthesis。
6. 布线。点击Run Implementation。
7. 生成Bit文件。点击Generate bit file。
8. 连接开发板。连接开发板的JTAG接口到电脑，给开发板上电。
9. 把Bit文件烧录到开发板。点击Hardware Manager，选择自动连接，会多出来两个图标，一个arm，一个FPGA。右键点击FPGA，选择Program Device，点击Program即完成烧写。
10. LED灯开始交替闪烁，表明实验成功。