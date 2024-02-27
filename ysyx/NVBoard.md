<!-- GFM-TOC -->
- [文件目录](#文件目录)
  - [$(NVBOARD\_HOME)文件目录](#nvboard_home文件目录)
  - [example文件目录](#example文件目录)
- [Makefile注解](#makefile注解)
  - [$(NVBOARD\_HOME)/scripts/nvboard.mk](#nvboard_homescriptsnvboardmk)
  - [$(NVBOARD\_HOME)/example/Makefile](#nvboard_homeexamplemakefile)
- [将自己的项目接入NVBOARD](#将自己的项目接入nvboard)
- [学习资料](#学习资料)
<!-- GFM-TOC -->
---
## 文件目录

### $(NVBOARD_HOME)文件目录
* `$board`
  * `N4` : 引脚说明文件
* `bulid`
  * `*.o `: `$(NVBOARD_HOME)/src`下的源文件生成的目标文件
  * `*.d` : 每个目标文件的依赖文件
  * `nvboard.a` : 由所有目标文件生成的静态库
* `example` : nvboard组件头文件
* `include` : nvboard组件源文件
* `src` : 示例项目文件
* `scripts`
  * `auto_pin_bind.py` : 生成C++约束文件的python脚本
  * `nvboard.mk` : 生成nvboard静态库等
  
### example文件目录
* `constr` : `top.nxpc`是用户自己编写引脚约束文件，由py脚本生成相应的C++约束文件供verilator的`wrapper file`调用
* `csrc` : `main.cpp`就是`wrapper file`，用户自己编写
* `vsrc` : 一系列用户自己编写的.v文件
* `resource` : `picture.hex`是nvboard的屏幕要显示的图片
* `build` :  
  * `top` : verilator生产的可执行文件
  * `auto_bind.cpp`:C++引脚约束文件，供verilator的`wrapper file`调用
  * `obj_dir`:verilator编译过程中的中间文件
---

## Makefile注解
### $(NVBOARD_HOME)/scripts/nvboard.mk
* **作用** ： 生成`静态库nvboard.a`和`编译选项CXXFLAGS`、`链接选项LDFLAGS`
* `$(NVBOARD_BUILD_DIR)/%.o: $(NVBOARD_SRC)/%.cpp`：
  * 在`$(NVBOARD_HOME)/src`下的每个.c文件都在`$(NVBOARD_HOME)/build`下生成相应的.o文件
  * `CXX` = `g++`
  * `CXXFLAGS`中的`MMD`意味着自动所有源文件所包含的头文件，并将其写入进相应的`.d依赖文件`当中。`.d文件`位于`$(NVBOARD_HOME)/build`目录中。
  * `$(shell sdl2-config --cflags)`不是很理解，大致就是要使用sdl库则需要为编译提供必要的选择项
* `$(NVBOARD_ARCHIVE): $(NVBOARD_OBJS)`:
  * 将`$(NVBOARD_HOME)/build`下所有的`.o文件`制成静态库 `$(NVBOARD_HOME)/build/nvboard.a`
* `-include $(NVBOARD_OBJS:.o=.d)`:
  * 通常用 `-include` 来代替 `include` 来忽略文件不存在或者是无法创建的错误提示。
  * `$(NVBOARD_OBJS:.o=.d)` : `$(var:a=b) 或 ${var:a=b}`的含义是把变量var中的每一个值结尾用b替换掉a

### $(NVBOARD_HOME)/example/Makefile
* `TOPNAME = top`:
  * 确定项目中的顶层文件名`top`，可以任意起名。
* `$(SRC_AUTO_BIND): $(NXDC_FILES)`:
  * 将用户自己编写引脚约束文件，通过py脚本生成相应的C++约束文件供verilator的`wrapper file`调用
* `$(BIN): $(VSRCS) $(CSRCS) $(NVBOARD_ARCHIVE)`
  * 使用`verilator`命令编译
  * 将中间过程文件放在`(NVBOARD_HOME)/build/obj_dir`目录下
  * 将**可执行文件**`$(TOPNAME)`放在`(NVBOARD_HOME)/build`目录下
---

## 将自己的项目接入NVBOARD
* 接入步骤：
  * 修改`vsrc/`中的`rtl文件`
  * 修改`csrc/`中的仿真文件`main.cpp`
  * 修改`constr/`中的引脚约束文件`top.nxdc`
* C++仿真文件模板
    ```c++
    #include <nvboard.h>

    // ...
    //调用该文件中的nvboard_bind_all_pins(dut)函数即可完成所有信号的绑定。
    nvboard_bind_all_pins(&dut);
    nvboard_init();

    while (1) {
    // ...
    nvboard_update();
    }

    nvboard_quit();
    ```
* 需要将`$(NVBOARD_ARCHIVE)`加入链接过程，并添加链接选项-lSDL2 -lSDL2_image
---

## 学习资料
1. [CSDN : makefile中的默认命令和默认参数-CXX和CXXFLAGS等](https://blog.csdn.net/weixin_34910922/article/details/119360790)
2. [CSDN : Linux Makefile 生成 *.d 依赖文件以及 gcc -M -MF -MP 等相关选项说明](https://blog.csdn.net/QQ1452008/article/details/50855810)
3. [CSDN : gcc 静态库制作之ar命令使用](https://blog.csdn.net/chen1415886044/article/details/104395351)
4. [CSDN : 浅显易懂 Makefile 入门 （09）— include 文件包含、MAKECMDGOALS](https://blog.csdn.net/wohu1104/article/details/111085894)
5. [CSDN : MakeFile .o .d文件放入指定的文件夹](https://blog.csdn.net/qinglongqishi1/article/details/80419332)