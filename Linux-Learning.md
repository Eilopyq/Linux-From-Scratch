# Linux-Learning

**本文主要用于记载操作系统以及 Linux 学习。**

**参考书籍：**鸟叔的Linux私房菜（基础学习篇）

## 第零章 计算机概论

### 0.1 计算机的五大单元

关于计算机的硬件部分，我们可以将其分为以下三个部分：

- 输入单元：键盘、鼠标等。
- 系统单元：CPU、内存等。
- 输出单元：屏幕、打印机等。

整个系统单元的关键在于 CPU（中央处理器），CPU 内又可分为两个主要的单元：

- 算数逻辑单元：程序运算与逻辑判断。
- 控制单元：协调各周边元件与各单元间的工作。

在计算机的实际工作过程中，由 CPU 中的**控制单元**发布指令使得**输入单元**的数据流入**内存**，再由**算术逻辑单元**进行计算，**控制单元**将所得的结果通过指令流入**内存**，最后再通过**输出单元**进行显示。

### 0.2 CPU 的架构

我们所使用的软件都要经过 CPU 内部的微指令集达成，这些指令集的设计主要又被分为两种设计理念，也是目前世界上常见到的两种 CPU 架构，即**精简指令集（RISC）**和**复杂指令集（CISC）**系统。

1. 精简指令集（RISC）

在这种CPU的设计中，微指令集较为精简，每个指令的执行时间都很短，完成的动作也很单纯，指令的执行性能较佳；但若要做复杂的事情，就要多个指令来完成。常见的 RISC 微指令集 CPU 主要包括：

- 甲骨文（Oracle）公司的SPARC系列，用于学术领域的大型工作站或银行金融体系的主要服务器。
- IBM 公司的 Power Architecture 系列，比如 SONY Play Station。
- ARM 公司的 ARM CPU系列，各大厂商的手机、导航设备、网络设备（交换器、路由器等）以及苹果公司的 M1 系列芯片均为 ARM 架构的 CPU 。

2. 复杂指令集（CISC）

相较于RISC，CISC在微指令集的每个小指令可以执行一些较低阶的硬件操作，指令数目多而且复杂，每条指令的长度并不相同。因为指令执行较为复杂所以每条指令花费的时间较长，但每条个别指令可以处理的工作较为丰富。常见的 CISC 微指令集 CPU主要包括 AMD 以及 Intel等的 X86 架构的 CPU。

> 关于 x86 架构的由来：最早的那颗 Intel 发展出来的 CPU 代号称为 8086，后来依此架构又开发出80286，80386....，因此这种架构的 CPU 也就被称为 x86 架构了。
>
> 此外64位的 x86 架构的 CPU 又被称为 x86_64。
