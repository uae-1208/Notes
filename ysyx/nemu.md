<!-- GFM-TOC -->
- [关键信息摘录](#关键信息摘录)
  - [menuconfig](#menuconfig)
  - [nemu\_trap](#nemu_trap)
- [我的注解](#我的注解)
  - [Makefile](#makefile)
  - [存储](#存储)
  - [NEMU模拟器框架](#nemu模拟器框架)
  - [NEMU指令执行框架](#nemu指令执行框架)
  - [NEMU的nemu\_trap机制](#nemu的nemu_trap机制)
- [学习资料](#学习资料)
<!-- GFM-TOC -->


---
## 关键信息摘录
### menuconfig
* 运行命令`mconf nemu/Kconfig`, 此时`mconf`将会解析`nemu/Kconfig`中的描述, 以菜单树的形式展示各种配置选项, 供开发者进行选择
* 退出菜单时, `mconf`会把开发者选择的结果记录到`nemu/.config`文件中
* 运行命令conf --syncconfig nemu/Kconfig, 此时conf将会解析`nemu/Kconfig`中的描述, 并读取选择结果`nemu/.config`, 结合两者来生成如下文件:
    * 可以**被包含到C代码中的宏定义**(`nemu/include/generated/autoconf.h`), 这些宏的名称都是形如`CONFIG_xxx`的形式
    * 可以**被包含到Makefile中的变量定义**(`nemu/include/config/auto.conf`)
* 所以, 目前我们只需要关心配置系统生成的如下文件:
  * `nemu/include/generated/autoconf.h`, 阅读C代码时使用
  * `nemu/include/config/auto.conf`, 阅读Makefile时使用
* 在`nemu/src`及其子目录下存在一些名为`filelist.mk`的文件, 它们会根据`menuconfig`的配置对如下4个变量进行维护
  * `SRCS-y` - 参与编译的源文件的候选集合
  * `SRCS-BLACKLIST-y` - 不参与编译的源文件的黑名单集合
  * `DIRS-y` - 参与编译的目录集合, 该目录下的所有文件都会被加入到`SRCS-y`中
  * `DIRS-BLACKLIST-y` - 不参与编译的目录集合, 该目录下的所有文件都会被加入到`SRCS-BLACKLIST-y`中

### nemu_trap
* 客户程序执行了`nemu_trap`指令. 这是一条虚构的特殊指令, 它是为了在NEMU中让客户程序指示执行的结束而加入的, NEMU在ISA手册中选择了一些用于调试的指令, 并将`nemu_trap`的特殊含义赋予它们. 例如在`riscv32`的手册中, NEMU选择了`ebreak`指令来充当`nemu_trap`. 为了表示客户程序是否成功结束, `nemu_trap`指令还会接收一个表示结束状态的参数. 当客户程序执行了这条指令之后, NEMU将会根据这个结束状态参数来设置NEMU的结束状态, 并根据不同的状态输出不同的结束信息, 主要包括：
  * `HIT GOOD TRAP` - 客户程序正确地结束执行
  * `HIT BAD TRAP` - 客户程序错误地结束执行
  * `ABORT` - 客户程序意外终止, 并未结束执行



---
## 我的注解
### Makefile
* `make run` ：该命令位于路径`$(NEMU_HOME)/scripts/native.mk`下，执行的操作命令为`$(BINARY) $(ARGS) $(IMG)`
  * `$(BINARY)` ：nemu的可执行文件
  * `$(ARGS)` ：默认执行参数，包括`--log=$(BUILD_DIR)/nemu-log.txt`
  * `$(IMG)` ：镜像文件(我的理解是想我们让nemu运行的二进制文件)。
    * 当用户在`make run`时不提供镜像文件，则使用默认内置镜像`(default build-in image)`，而该文件为空文件。
  
### 存储
  * 模拟CPU的存储器是`在nemu/src/memory/paddr.c`中定义的大数组`pmem[0x8000000]`，大小为**128MB**
  * `guest_to_host` ：将客户计算机的**物理地址**转换为**虚拟地址**
  * `RESET_VECTOR` ：CPU**运行客户程序**时，从该地址开始执行。默认为`0x80000000`。

### NEMU模拟器框架
* `init_monitor()` ：初始化monitor
  * `parse_args(argc, argv)` ：解析程序运行时传入的参数
    * 首先自定义了一些允许接受的参数，并存放于`table`中
      * `-b,--batch`       :  run with batch mode\n"
      * `-l,--log=FILE`    :  output log to FILE\n"
      * `-d,--diff=REF_SO` :  run DiffTest with reference REF_SO\n"
      * `-p,--port=PORT`   :  run DiffTest with port PORT\n"
      * `-h,--help`
    * 然后调用GNU/Linux提供的函数`getopt_long()`，用于处理运行程序时**输入的参数**
  * `init_rand()` ：初始化随机数种子
  * `init_log(log_file)` ：初始化日志文件
    * 打开日志文件`log_file`，该变量的值由`getopt_long()`函数解析后为`$(BUILD_DIR)/nemu-log.txt`，见[Makefile](#makefile)中的`make run`部分
  * `init_mem()` ：将模拟CPU的存储器都以**相同的随机数**初始化
  * `IFDEF(macro, abc)` ：如果宏`macro`被定义了，则执行语句`abc`（可见[神奇的宏定义](https://www.cnblogs.com/zhangyi1357/p/16192431.html)）
  * `init_isa()` ：根据特定的CPU架构进行初始化
    * `memcpy()` ：加载一段非常简单的客户程序指令，该指令位于32位数组`img`当中
    * `restart()` ：复位`PC`
  * `long img_size = load_img()` ：将用户提供的镜像文件(在`make run`命令中由`-l`参数导入)从`RESET_VECTOR`地址开始写入存储器。若用户不提供，则使用`default build-in image`。
  * `load_elf()` ：导入elf文件并初始化，由uae添加。
  * `init_difftest()` ：初始化DiffTest
    * `handle = dlopen(ref_so_file, RTLD_LAZY)` ：打开动态库文件并存入`handle`。
    * `dlsym` x 5 : 从动态库中获取符号函数地址，这些函数都在动态库中声明和定义。
      * `ref_difftest_memcpy()`
      * `ref_difftest_regcpy()`
      * `ref_difftest_exec()`
      * `ref_difftest_raise_intr()`
      * `ref_difftest_init()`
    * `ref_difftest_init(port);` ：初始化`REF`
    * `ref_difftest_memcpy(RESET_VECTOR, guest_to_host(RESET_VECTOR), img_size, DIFFTEST_TO_REF)` ： 利用`DUT`的`image`文件初始化`REF`的内存。
    * `ref_difftest_regcpy(&cpu, DIFFTEST_TO_REF)` ：利用`DUT`的寄存器值初始化`REF`的寄存器。
  * `init_sdb()` ：初始化简易调试器
    * `init_regex()` ：初始化正则表达式相关规则
      * `regcomp` ：将用于匹配表达式中每一个`token`的`pattern`转换成C语言能处理的格式。
        * 这种格式的数据存储于类型为`regex_t`的变量，即`re`数据的成员。
        * `rules`的数组则存储所有的`pattern`。
      * `regerror` ：若函数`regcomp`未正常工作，则存储相应的错误信息。
    * `init_wp_pool()` ：
  * `welcome()` ：输出欢迎信息
    * 以及trace的状态信息
    * 还输出了编译的时间和日期
* `engine_start()` 
  * `sdb_mainloop()` ：不断地从用户那里接收命令，直至用户输入`q`。
    * `is_batch_mode`：如果用户设置为批处理模式`batch mode`，则执行`cmd_c(NULL)`，即直接运行`image`。否则往下运行`sdb`。
    * `rl_gets()` ：读取`(nemu) `后的字符串
    * `cmd`指向的是读取到的**命令**字符串
    * `args`指向的是读取到的**参数**字符串
    * `cmd_table`存储的是`nemu`可以执行的命令
      * `cmd_help`、`cmd_c`和`cmd_q`是函数指针。
* `is_exit_status_bad()` ：       

### NEMU指令执行框架
* <u>***`cpu_exec(n)`***</u> ：执行n条指令
  * `g_print_step` ： n不超过`MAX_INST_TO_PRINT`时则打印指令信息。
  * [NEMU的nemu_trap机制](NEMU的nemu_trap机制)
  * <u>***`execute(n)`***</u> ：
    * 执行指令总数递增。
    * <u>***`exec_once(&s, cpu.pc)`***</u> ：
      * `s->pc`和`s->snpc`指向`cpu.pc`。
      * <u>***`isa_exec_once(s)`***</u> ：根据特定的CPU架构执行指令。
        * <u>***`inst_fetch(&s->snpc, 4)`***</u> ：
          * 取指存放于`s->isa.inst.val`。
          * 更新`s->snpc`指向下一条指令 ：`s->snpc += 4/8`
        * <u>***`decode_exec(s)`***</u> 
          * `s->dnpc`指向`s->snpc`。
          * `INSTPAT_START()`  ：使变量`__instpat_end`指向标签`__instpat_end_`的地址
          * `INSTPAT(xxx)`  
            * `pattern_decode` ： 可能使用了某种匹配算法根据输入的`pattern`生成特定的`key、mask、shift`。每一条指令都有**独一无二的`pattern`**。
            * `if...` ： 判断读取到的当前指令是否符合当前`pattern`（利用`key、mask、shift`）。
            * 若**符合**，则`INSTPAT_MATCH` 
              * `decode_operand` ： 取寄存器、立即数。
              * 执行指令。
              * `goto...` ：跳转到`INSTPAT_END`，结束`decode_exec()`。
            * 若**不符**，则执行下一条`INSTPAT(xxx)`，直至遍历匹配规则，报错。
          * `INSTPAT_END()`  ： 存放标签`__instpat_end_`。
      * `cpu.pc`指向`s->dnpc`
      * `CONFIG_ITRACE`部分：向`s->logbuf`写入当前执行的指令的地址和机器码。
      * 如果未定义`CONFIG_ISA_loongarch32r`，则<u>***`disassemble(......)`***</u>
        * 使用`llvm`对当前指令的机器码进行反汇编操作。
        * 向`s->logbuf`写入反汇编结果。
    * `g_nr_guest_inst ++`
    * <u>***`trace_and_difftest(&s, cpu.pc)`***</u>
      * 若定义了`CONFIG_ITRACE_COND`，则将`s->logbuf`写入进log中。
      * n不超过`MAX_INST_TO_PRINT`时，则在终端中输出`s->logbuf`。
      * `difftest_step(_this->pc, dnpc)`：进行difftest
        * `ref_difftest_exec(1);` ： 使`REF`执行一条指令。
        * `ref_difftest_regcpy(&ref_r, DIFFTEST_TO_DUT);` ： 将`REF`的寄存器状态复制给`ref_r`。
        * `checkregs(&ref_r, pc, npc);` ：检查`DUT`和`REF`的所有寄存器值是否相同，不相同则终止`DUT`的运行。
  * 更新时间和`nemu_state`
  * `statistic(n)` ：统计执行情况
    * CPU结束状态
    * 执行客户程序所花费的时间
    * 执行的指令数量
    * 平均执行频率（MIPS）

### NEMU的nemu_trap机制
* `nemu_state.state`的取值范围：
  * `NEMU_RUNNING`：表示NEMU处于执行指令的状态。
  * `NEMU_STOP`：表示sdb要求NEMU执行的指令已经执行完毕，暂停一下。
  * `NEMU_END`：表现NEMU执行了ebreak指令。
  * `NEMU_ABORT`：表现NEMU遇到了未定义指令或者执行错误。
  * `NEMU_QUIT`：
* 当sdb命令NEMU执行指令时：
  * 在`cpu_exec()`中，首先判断一下`nemu_state.state`的取值：
    * 若为`NEMU_END`或`NEMU_ABORT`，则退出函数`cpu_exec()`。
    * 否则，执行`nemu_state.state = NEMU_RUNNING`。
  * 然后在`execute(n)`中，在for循环中调用`exec_once`，然后判断`nemu_state.state == NEMU_RUNNING`，否则继续执行，不是则中断for循环。
    * 当执行`ebreak`指令时，实际上执行：
      * `nemu_state.state = NEMU_END`；
      * `nemu_state.halt_pc = pc`；
      * `nemu_state.halt_ret`被设置为`$a0`中的值，即运行于nemu上的程序的main函数返回值。
  * 再次`switch (nemu_state.state)`：
    * 若为`NEMU_RUNNING`，则设置为`NEMU_STOP`。
    * 若为`NEMU_ABORT`，则打印`ABORT`。
    * 若为`NEMU_END`：
      * nemu_state.halt_ret == 0，则打印`HIT GOOD TRAP`。（main函数`return 0`）
      * nemu_state.halt_ret != 0，则打印`HIT BAD TRAP`。（main函数没有`return 0`）
    * 若为`NEMU_QUIT`，则`statistic()`。
  


---
## 学习资料
1. [C/C++ 命令解析：getopt 方法详解和使用示例](https://blog.csdn.net/afei__/article/details/81261879)
2. [浅谈linux的命令行解析参数之getopt_long函数](https://blog.csdn.net/qq_33850438/article/details/80172275)
3. [C语言 ##__VA_ARGS__](https://zhuanlan.zhihu.com/p/410584465?utm_id=0)
4. [神奇的宏定义](https://www.cnblogs.com/zhangyi1357/p/16192431.html)
5. [C语言__attribute__的使用](https://blog.csdn.net/qlexcel/article/details/92656797)
6. [C 库函数 - fseek()](https://www.runoob.com/cprogramming/c-function-fseek.html)
7. [C 库函数 - ftell()](https://www.runoob.com/cprogramming/c-function-ftell.html)
8. [strtok()函数详解！](https://blog.csdn.net/weibo1230123/article/details/80177898)
9. [C语言用regcomp、regexec、regfree和regerror函数实现正则表达式校验 ](https://www.cnblogs.com/liudw-0215/p/9724347.html)
10. [C语言正则表达式详解 regcomp() regexec() regfree()详解](https://blog.csdn.net/derkampf/article/details/70661551)
11. [C语言printf中%s、%*s、%*.*s的作用以及实现一个进度条](https://blog.csdn.net/bjbz_cxy/article/details/126799481)
12. [巴科斯范式（Backus–Naur Form）介绍](https://www.cnblogs.com/BstuderBlog/p/16151372.html)
13. [printf 改变输出颜色](https://blog.csdn.net/hhtang/article/details/4726821)
14. [C语言带颜色的printf/fprintf打印](https://blog.csdn.net/ericbar/article/details/79652086)
15. [C语言中的逻辑右移和算术左移](https://blog.csdn.net/zyings/article/details/47084485)
16. [goto语句中的标签地址](https://blog.csdn.net/fjb2080/article/details/5248359)
17. [Labels as Values](https://gcc.gnu.org/onlinedocs/gcc/Labels-as-Values.html)
18. [dlopen系列函数详解](https://zhuanlan.zhihu.com/p/560349203)
19. [动态库加载函数dlsym 在C/C++编程中的使用](https://blog.csdn.net/xuedaon/article/details/123401531)
   