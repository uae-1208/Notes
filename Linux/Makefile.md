<!-- GFM-TOC -->
- [常用函数](#常用函数)
- [变量与赋值](#变量与赋值)
- [make参数](#make参数)
- [其他](#其他)
- [学习资料](#学习资料)
<!-- GFM-TOC -->
---

## 常用函数
1. `wildcard`：进行模式匹配
  ```bash
    $ echo '$(wildcard *.c)
  ```
2. `patsubst`：进行模式替换
  ```bash
    $ echo '$(patsubst %.c %.o file_list)
    #file_list列举的所有.c文件名均被替换成.o文件名
  ```
3. `foreach`：类似for循环
  ```bash
  $ $(foreach VAR,LIST,TEXT)
  #f执行时把"LIST"中使用空格分隔的字符串依次取出赋值给变量"VAR"，
  #然后执行"TEXT"表达式。重复直到"LIST"的最后一个字符串。
  ```
4. `notdir`：去除绝对路径中的目录，只保留文件名
  ```bash
    $ echo '$(notdir ~/ysyx/ysyx-workbench/init.sh)
    #~/ysyx/ysyx-workbench/init.sh  -->  init.sh
  ```
5. `dir`：去除绝对路径中的目录，只文件名
  ```bash
  $ echo '$(dir ../temp/init.sh)
  ../temp/
  #无论该Makefile位于何目录下，展示的都是“../temp/”
  #而不会将“..”展开
  ```
6. `suffix`：取后缀
  ```bash
    $ echo '$(suffix a/init.sh temp.cpp)
    .sh .cpp
  ```
7. `basename`：取前缀
  ```bash
    $ echo '$(basename a/init.sh temp.cpp)
    a/init temp
  ```
8. `addsuffix`：添加后缀
  ```bash
    $ echo '$(addsuffix .sh, a/init temp.cpp)
    a/init.sh temp.cpp.sh
  ```
9. `addprefix`：添加前缀
  ```bash
    $ echo $(addprefix src/, a/init.sh temp.cpp)
    src/a/init.sh src/temp.cpp
  ```
10. `abspath` = `realpath`：提取绝对路径
  ```bash
    $ echo '$(abspath npc)
    /home/uae/ysyx/ysyx-workbench/npc
  ```
11. `join`
12. `subst`


---
## 变量与赋值
* `$@`，表示规则中的`目标`。
* `$<`，表示规则中的`第一个条件`。
* `$?`，表示规则中所有`比目标新的条件`，组成一个列表，以空格分隔。
* `$^`，表示规则中的`所有条件`，组成一个列表，以空格分隔。 
* `$*`,表示目标模式中"%"及其之前的部分。如果目标是`dir/a.foo.b`，并且目标的模式是`a.%.b`，那么，`$*`的值就是`dir /a.foo`。

---
## make参数
* `-s`:在命令运行时不输出命令的输出。
* `-C`:指定读取makefile的目录。
* `make xx -nB | code -` ：观察具体执行了哪些指令

---
## 其他
* 如果`make`执行的命令前面加了`@`字符，则不显示命令本身而只显示它的结果；
* 通常`make`执行的命令如果出错（该命令的退出状态非0）就立刻终止，不再执行后续命令，但如果命令前面加了`-`号，即使这条命令出错，`make`也会继续执行后续命令。
  * 通常`rm`命令和`mkdir`命令前面要加`-`号，因为`rm`要删除的文件可能不存在，`mkdir`要创建的目录可能已存在，这两个命令都有可能出错，但这种错误是应该忽略的。
* `%.o : %.c` : `%.o` 的作用是匹配所有以 `.o` 结尾的目标；而后面的 `%.c` 中 `%` 的作用，则是将 `%.o` 中 `%` 的内容原封不动的挪过来用。
* 如果一个目标拆开写多条规则，其中只有一条规则允许有命令列表，其它规则应该没有命令列表，否则`make`会报警告并且采用最后一条规则的命令列表。
* 通常把`CFLAGS`定义成一些编译选项，例如`-O`、`-g`等，而把`CPPFLAGS`定义成一些预处理选项，例剥离文件的绝对路径，只保留文件名如`-D`、`-I`等。
* `隐藏规则` ：[2. 隐含规则和模式规则](https://www.bookstack.cn/read/linux-c/09e2fe661ce26662.md)
* **通过日志来观察makefile的行为**：`make -d ... `
  [NEMU代码导读 [第六期“一生一芯”计划 - P7]  18min50s](https://www.bilibili.com/video/BV1up4y1j7Ji/?spm_id_from=333.788.top_right_bar_window_history.content.click&vd_source=d791a57f43dad7ca6a1d62950cab7001)
* `make xx -nB | code -` ：观察具体执行了哪些指令
 
---
## 学习资料
1. [CSDN : Makefile中patsubst函数使用方法](https://blog.csdn.net/yanlaifan/article/details/71402787)
2. [CSDN : Makefile学习3 - foreach函数](https://blog.csdn.net/to_be_better_wen/article/details/130038966)
3. [CSDN : Makefile中notdir函数使用方法](https://blog.csdn.net/yanlaifan/article/details/71402795)
4. [CSDN : Makefile（06）— 文件名操作函数（dir、notdir、suffix、basename、addsuffix、addperfix、join、wildcard）](https://blog.csdn.net/stephenbruce/article/details/130040336)
5. [Makefile中的subst函数](https://blog.csdn.net/pure_dreams/article/details/79976367)
6. [Linux之Makefile(join)](https://blog.csdn.net/zhoudengqing/article/details/41778267)
7. [Makefile教程（绝对经典，所有问题看这一篇足够了）](https://blog.csdn.net/weixin_38391755/article/details/80380786)
