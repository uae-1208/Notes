* 在Cortex-A53架构上，L1 cache分为单独的instruction cache（ICache）和data cache（DCache）。L1 cache是CPU私有的，每个CPU都有一个L1 cache。一个cluster 内的所有CPU共享一个L2 cache，L2 cache不区分指令和数据，都可以缓存。所有cluster之间共享L3 cache。L3 cache通过总线和主存相连。
* CPU要访问的数据在cache中有缓存，称为“命中” (hit)，反之则称为“缺失” (miss)。
* 某一地址的数据可能存在多级缓存中。这种多级cache的工作方式称之为inclusive cache。与inclusive cache对应的是exclusive cache，这种cache保证某一地址的数据缓存只会存在于多级cache其中一级。也就是说，任意地址的数据不可能同时在L1和L2 cache中缓存。
* 我们将cache平均分成相等的很多块，每一个块大小称之为cache line，其大小是cache line size。这里有一点需要注意，cache line是cache和主存之间数据传输的最小单位。
* tag取自虚拟地址导致歧义，index取自虚拟地址导致别名。 ？？？？？


MMU将VA映射到PA是以页（Page） 为单位的
每一个进程都拥有一个自己的页表