<!-- GFM-TOC -->
- [关键信息摘录](#关键信息摘录)
- [我的注解](#我的注解)
  - [Makefile](#makefile)
    - [相关变量](#相关变量)
    - [make命令执行流程](#make命令执行流程)
  - [链接、汇编](#链接汇编)
- [学习资料](#学习资料)
<!-- GFM-TOC -->

---
## 关键信息摘录
* 在让NEMU运行客户程序之前, 我们需要将客户程序的代码编译成可执行文件。需要说明的是, 我们不能使用gcc的默认选项直接编译, 因为默认选项会根据GNU/Linux的运行时环境将代码编译成运行在GNU/Linux下的可执行文件。但此时的NEMU并不能为客户程序提供GNU/Linux的运行时环境, 在NEMU中无法正确运行上述可执行文件, 因此我们不能使用gcc的默认选项来编译用户程序。
* 解决这个问题的方法是交叉编译. 我们需要在GNU/Linux下根据AM的运行时环境编译出能够在`$ISA-nemu`这个新环境中运行的可执行文件。为了不让链接器ld使用默认的方式链接, 我们还需要提供描述`$ISA-nemu`的运行时环境的链接脚本。AM的框架代码已经把相应的配置准备好了, 上述编译和链接选项主要位于`abstract-machine/Makefile`以及`abstract-machine/scripts/`目录下的相关.mk文件中. 编译生成一个可以在NEMU的运行时环境上运行的程序的过程大致如下:
   - gcc将`$ISA-nemu`的AM实现源文件编译成目标文件, 然后通过ar将这些目标文件作为一个库, 打包成一个归档文件`abstract-machine/am/build/am-$ISA-nemu.a`
   - gcc把应用程序源文件(如`am-kernels/tests/cpu-tests/tests/dummy.c`)编译成目标文件
   - 通过gcc和ar把程序依赖的运行库(如`abstract-machine/klib/`)也编译并打包成归档文件
   - 根据Makefile文件`abstract-machine/scripts/$ISA-nemu.mk`中的指示, 让ld根据链接脚本`abstract-machine/scripts/linker.ld`, 将上述目标文件和归档文件链接成可执行文件


---
## 我的注解
### Makefile
#### 相关变量
- `/home/uae/ysyx/ysyx-workbench/am-kernels/kernels/hello/Makefile` :
    - NAME = `hello`
    - SRCS = `hello.c`
- `/home/uae/ysyx/ysyx-workbench/abstract-machine/Makefile` :
    - ARCH_SPLIT = `riscv32 nemu`
    - ISA = `riscv32`
    - PLATFORM = `nemu`
    - WORK_DIR = `/am-kernels/kernels/hello`
    - DST_DIR = `/am-kernels/kernels/hello/build/riscv32-nemu`
    - IMAGE_REL = `build/hello-riscv32-nemu`
    - IMAGE = `hello-riscv32-nemu`
    - ARCHIVE = `/am-kernels/kernels/hello/build/hello-riscv32-nemu.a`
    - OBJS = `/am-kernels/kernels/hello/build/hello.o`
    - LIBS = `am` + `klib`
    - LINKAGE = `abstract-machine/am/build/am-riscv32-nemu.a` + `abstract-machine/klib/build/klib-riscv32-nemu.a` 
    - INC_PATH = `/am-kernels/kernels/hello/include` + `abstract-machine/am/include/` + `abstract-machine/klib/include/`     
    - INCFLAGS = `-I /am-kernels/kernels/hello/include` + `-I abstract-machine/am/include/` + `-I abstract-machine/klib/include/` 
    - ARCH_H = arch/riscv.h  //uae

#### make命令执行流程
- 命令：`make ARCH=riscv32-nemu run`
1. `$(AM_HOME)/scripts/platform/nemu.mk`：
   1. 跳转至`run: image`：存在依赖`image`，检查其是否有更新。
   2. 跳转至`image: $(IMAGE).elf`：存在依赖`$(IMAGE).elf`，检查其是否有更新。 ----> 目标`$(IMAGE).elf`位于`$(AM_HOME)/Makefile`。
2. `$(IMAGE).elf`位于`$(AM_HOME)/Makefile`：
   1. 跳转至`$(IMAGE).elf: $(OBJS) $(LIBS)`：
      1. 存在依赖`$(OBJS)`，检查其是否有更新。
      2. `$(DST_DIR)/%.o: %.c`：若依赖有更新，则执行：
         1. `@mkdir -p $(dir $@) && echo + CXX $<`
         2. `@$(CXX) -std=c++17 $(CXXFLAGS) -c -o $@ $(realpath $<)`
      3. 存在依赖`$(LIBS)`，检查其是否有更新。
      4. `$(LIBS): %:`：若依赖有更新，则执行：
         1. `@$(MAKE) -s -C $(AM_HOME)/$* archive`：相当于执行`make archive`命令，`$(AM_HOME)/$*`被替换为`$(AM_HOME)/am`和`$(AM_HOME)/klib`。
   2. 跳转至`archive: $(ARCHIVE)`：存在依赖`$(ARCHIVE)`，检查其是否有更新。
   3. 跳转至`$(ARCHIVE): $(OBJS)`：存在依赖`$(OBJS)`，检查其是否有更新。（此时的`$(OBJS)`似乎因为.d的原因变成了`am和klib`下的.o，此处尚不太明）
      1. 若依赖有更新，则执行:
         1. `@echo + AR "->" $(shell realpath $@ --relative-to .)`
         2. `@$(AR) rcs $(ARCHIVE) $(OBJS)`
   4. `$(IMAGE).elf: $(OBJS) $(LIBS)`的依赖更新完毕，执行该目标下的命令：
      1. `@echo + LD "->" $(IMAGE_REL).elf`
      2. `@$(LD) $(LDFLAGS) -o $(IMAGE).elf --start-group $(LINKAGE) --end-group`
   5. `image: $(IMAGE).elf`的依赖更新完毕，跳回至`$(AM_HOME)/scripts/platform/nemu.mk`
3. `$(AM_HOME)/scripts/platform/nemu.mk`：
   1. 执行`image: $(IMAGE).elf`下的命令：
      1. `@$(OBJDUMP) -d $(IMAGE).elf > $(IMAGE).txt`
      2. `@echo + OBJCOPY "->" $(IMAGE_REL).bin`
      3. `@$(OBJCOPY) -S --set-section-flags .bss=alloc,contents -O binary $(IMAGE).elf $(IMAGE).bin`
4. `run: image`的依赖更新完毕，现在执行该目标下的命令：
   1. `$(MAKE) -C $(NEMU_HOME) ISA=$(ISA) run ARGS="$(NEMUFLAGS)" IMG=$(IMAGE).bin`
      1. 该命令跳转至目录`$(NEMU_HOME)`并执行`make run`命令运行nemu，**若使nemu运行于批处理模式，则可修改变量`$(NEMUFLAGS)`**


### 链接、汇编
- 在`nemu.mk`中的`LDFLAGS`定义了
  - `_pmem_start=0x80000000`；
  - `_entry_offset=0x0`。
- 在`linker.ld`中的定义了
  - `_entry`为`_start`；
  - `_stack_top`；
  - `stack`的大小为`0x8000`
  - `_heap_start`。
- 在`start.S`中的定义了`_start`：
  - `mv s0, zero`；
  - `la sp, _stack_pointer`：使`$sp`指向`_stack_pointer`；
  - `jal _trm_init`：跳转至`_trm_init`。
- 在`trm.c`中定义了`_trm_init()`：
  - `int ret = main(mainargs);`
  - `halt(ret);`


---
## 学习资料
1. [linux 源码中__asm__ __volatile__作用](https://blog.csdn.net/yt_42370304/article/details/84982864)
2. [C 标准库 - <stdarg.h>](https://www.runoob.com/cprogramming/c-standard-library-stdarg-h.html)
3. [C 库函数 - vsprintf()](https://www.runoob.com/cprogramming/c-function-vsprintf.html)
4. [C 库函数 - sprintf()](https://www.runoob.com/cprogramming/c-function-sprintf.html)