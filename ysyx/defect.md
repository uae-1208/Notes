# 当前NEMU的缺陷

- 表达式求值
  1. 有效token不能超过**50**个
  2. 对于**十六进制**数字，只能处理**0x**开头的数字，且当数字超过32bit时，自动截取低32bit。
     1. 当输入的数字过长时会导致`tokens`数组被占满。（因为缺陷1）
  3. 出现无效表达式时nemu**停止运行**，且无法详细显示无效原因。
     1. 如，输入`(0x1 + 0x2))` 时会显示`int op = get_prime(p, q);   assert(op != -1);`断言不通过。
     2. 再如，输入`(0x1 ++ 0x2)` 时会显示`if (p > q)  assert(0);`断言不通过。

- `mtrace`无法通过`menuconfig`打开或者关闭，只能通过`$NEMU_HOME/src/memory/vaddr.c`中的宏定义`CONFIG_MTRACE`来配置。

