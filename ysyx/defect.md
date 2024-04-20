# 当前NEMU的缺陷

## 表达式求值
  1. 有效token不能超过**50**个
  2. 对于**十六进制**数字，只能处理**0x**开头的数字，且当数字超过32bit时，自动截取低32bit。
     1. 当输入的数字过长时会导致`tokens`数组被占满。（因为缺陷1）
  3. 出现无效表达式时nemu**停止运行**，且无法详细显示无效原因。
     1. 如，输入`(0x1 + 0x2))` 时会显示`int op = get_prime(p, q);   assert(op != -1);`断言不通过。
     2. 再如，输入`(0x1 ++ 0x2)` 时会显示`if (p > q)  assert(0);`断言不通过。

## Trace
  - `iringbuf`、`mtrace`和`ftrace`目前都无法通过`menuconfig`打开或者关闭，只能通过`./include/common.h`中的下述宏定义来配置:
``` C
#define CONFIG_IRINGBUF 1
#define CONFIG_MTRACE   1
#define CONFIG_DTRACE   1
#define CONFIG_FTRACE   1
``` 
   - `ftrace`无法识别函数调用的`jalr`指令和普通的无条件跳转`jalr`指令。
