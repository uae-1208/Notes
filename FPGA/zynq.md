* 可以看到，只有两个 AXI-GP 是 Master Port，即主机接口，其余 7 个口都是 Slave Port（从机接口）。主机接口具有发起读写的权限，ARM 可以利用两个 AXI-GP 主机接口主动访问 PL 逻辑，其实就是把 PL 映射到某个地址，读写 PL 寄存器如同在读写自己的存储器。其余从机接口就属于被动接口，接受来自 PL 的读写，逆来顺受。
* GP 接口是 32 位的低性能接口，理论带宽600MB/s，而 HP 和 ACP 接口为 64 位高性能接口，理论带宽 1200MB/s。


