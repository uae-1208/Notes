设计发送方逻辑时，不能将 READY 信号作为置高 VALID 逻辑的条件，比如将 READY 信号通过组合逻辑生成 VALID 信号。

换句话说，发送方准备发送，置起 VALID 信号是完全主动与独立的过程。接收方 READY 信号按照协议可以依赖发送方 VALID 信号，但如果此时发送方也依赖接收方信号，就会造成死锁的情况，所以协议在这里强调了 VALID 信号的主动性。


 READY 信号原则上由接收方自身的接收状况以及 VALID 信号控制。（或者仅由接收方自身的接收状况决定）


协议建议 AW/AR READY 信号（这里 AW/AR 指的是读写地址通道的 READY 信号，将在第二章中正式引入）的默认电平为高电平。若默认电平为低，则每次传输至少需要 2 个周期才能完成，第一个周期置高 VALID 信号，第二个周期从机才会置高 READY 信号。相当于每次传输增加 1 个周期时间开销，这在某些情况下会对传输效率有较大的影响。

应当注意到，在 valid 置高的同时，发送方就应该给出有效数据，并将有效数据保持在总线，而在之后的 ACLK 上升沿，若 valid、ready 均有效，则应更新有效数据。


每一个通道都拥有自己的VALID与READY信号用于实现握手，其中VALID信号表示通道的地址、数据或控制信息已经可用，而READY信号则表示接收方已准备好接收信息，其中，读数据和写数据通道还拥有LAST信号，该信号用于指示当前传输是否为当前事务中的最后一次传输。



在 AXI4 中，INCR 类型最大支持长度为 256，其他类型最大长度为 16。而 AXI3 中这一数字无论何种模式均为 16。因此 AXI4 中 AxLen 信号位宽为 8bit，AXI3 中的 AxLen 则仅需要 4bit。协议中的 AxLen 信号从零开始表示，实际的长度值为 AxLen + 1。
    对于 WRAP 模式，突发传输长度仅能为2,4,8,16
    在一次突发传输中，地址不能跨越一个 4KB 分区
    一次突发传输不能在完成所有数据传输前提前结束（early termination）


突发传输宽度信号 AXSIZE 位宽为 3bit，表示为：
    传输宽度 = 2 ^ AXSIZE


FIXED 类型中， burst 中所有数据都使用起始地址。该模式适合对某个固定地址进行多次数据更新，比如读写一个 fifo 时，读写地址就是固定的。
INCR 类型最为常用，后续数据的地址在初始地址的基础上进行递增，递增幅度与传输宽度相同。适合对于 RAM 等通过地址映射（mapped memory）的存储介质进行读写操作。
WRAP 类型比较特殊，首先根据起始地址得到绕回边界地址（wrap boundary）与最高地址。当前地址小于最高地址时，WRAP 与 INCR 类型完全相同，地址递增。但到递增后的地址到达最高地址后，地址直接回到绕回边界地址，再进行递增，就这样循环往复。最高地址由绕回边界地址计算得到：wrap boundary + （N_bytes x burst_len）


当本次传输中数据位宽小于通道本身的数据位宽时，称为窄位宽数据传输，或者直接翻译成 窄传输。在窄位宽写传输中，主机需要告知从机数据通道中哪些字节是有效的，需要使用到写数据通道中的 WSTRB 信号。WSTRB 信号中的单个 bit 置起，表示对应位置上的字节有效，对应关系为：
WSTRB[n] 对应 WDATA[8n+7:8n]，也就是：当 WSTRB[n] 为 1 时，WDATA[8n+7:8n]有效。



单机多事务场景（假设多个事务能够被从机并行处理）
    1.超前传输（outstanding transaction）
    2.多事务乱序
        但是一般从机的数据准备时间不由主机控制，数据就绪顺序与事务到达顺序不一致是可能的。因此超前传输需要相应的机制来标识数据所属的事务。
        （1）协议规定，对于 ARID 一致的多个事务，从机必须按照接收事务的顺序，返回其读数据。
        （2）协议规定，具有不同 ARID 的事务之间可以乱序。
        对于支持不同 ARID 的从机来说，实现为每个 ARID 准备了一个事务与数据缓冲区。
         WID 信号在 AXI4 中被取消，这点会在后续的文章中详细讨论。（为什么！！！！！！！！！！！！！）
多主机场景
    多机互联中 AXI Interconnect 起到了最重要的作用。
    不同主机间的事务是独立的，之间没有顺序约束。
    中间节点对不同主机的 ID 进行调整，即使主机发出的 ID 一致，也能使从机看到的 ID 不同。
    从机端 ID 位宽需要设置地比主机端 ID 位宽更大。协议建议（而非强制）:
        主机端位宽不超过 4bit，Interconnect 为主机事务增加的 ID 位宽不超过 4bit
        因此从机端的 ID 位宽不超为 8bit