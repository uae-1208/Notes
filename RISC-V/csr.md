<!-- GFM-TOC -->
- [一些概念](#一些概念)
- [CSR的总结总结](#csr的总结总结)
- [Trap发生时，硬件上Hart的操作](#trap发生时硬件上hart的操作)
- [在此之后，软件上的操作](#在此之后软件上的操作)
- [执行MRET指令时，硬件上Hart的操作](#执行mret指令时硬件上hart的操作)
- [学习资料](#学习资料)
<!-- GFM-TOC -->
---

## 一些概念
* 异常控制流 (Exceptional Control Flow，简称 ECP)，包括：
  * exception
  * interrupt
* 在RISC-V中，`ECP`也被称为`Trap`。
  
## CSR的总结总结
* 具体翻阅`RISC-V/pic/ch10-trap-exception.pdf`。

## Trap发生时，硬件上Hart的操作
* 把 `mstatus` 的 `MIE` 值复制到 `MPIE` 中，清除 `mstatus` 中的 `MIE` 标志位，效果是`所有中断`被禁止。
* 设置 `mepc` ，同时 `PC` 被设置为 `mtvec`。（需要注意的是，对于 `exception`， `mepc` 指向导致异常的指令；对于 `interrupt`，它指向被中断的指令的下一条指令的位置。）
* 根据 trap 的种类设置 `mcause`，并根据需要为 `mtval` 设置附加信息。
* 将 trap 发生之前的权限模式保存在 `mstatus` 的 `MPP` 域中，再把 hart 权限模式更改为 M`（也就是说无论在任何 Level 下触发 trap hart 首先切换到 Machine 模式）`。
  
## 在此之后，软件上的操作
* 保存（save）当前控制流的上下文信息（利用 `mscratch`）。
* 调用 C 语言的 `trap_handler`。
* 从 `trap_handler` 函数返回，`mepc` 的值有可能需要调整。
* 恢复（restore）上下文的信息。
* 执行 `MRET` 指令返回到 `trap` 之前的状态。

## 执行MRET指令时，硬件上Hart的操作
* 针对不同权限级别下如何退出 trap ，有各自的返回指令 xRET(x = M/S/U)。
* 以在 `M 模式`下执行 `mret` 指令为例，会执行如下操作： 
  * 当前 Hart 的权限级别 = `mstatus.MPP`; `mstatus.MPP = U`（如果 hart 不支持 U 则为 M）。
  * `mstatus.MIE = mstatus.MPIE`; `mstatus.MPIE = 1`。
  * `pc = mepc`。

## 学习资料
1. [[完结] 循序渐进，学习开发一个RISC-V上的操作系统 - 汪辰 - 2021春](https://www.bilibili.com/video/BV1Q5411w7z5?p=20&vd_source=d791a57f43dad7ca6a1d62950cab7001)
2. [RVOS Exercise Notes IV](https://ludi.dev/posts/rvos-exercise-iv/)