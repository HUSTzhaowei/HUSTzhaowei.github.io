---
title: "Cache basics"
date: 2019-04-02
categories:
  - blog
tags:
  - cache
toc: true
---
 
# 1.cache基本概念
## 1.1Block
主存和cache间以及cache和CPU间复制数据的最小大小，通常为64B。
## 1.2cache hit&miss
以LLC为例，上级的cache如果在LLC中找到相应的块，就称为cache hit，就把相应的块复制到上级的cache中或者CPU中。相反，如果没有找到，就称为LLC miss，需要从主存里面复制相应的块到LLC或者直接到上级cache（根据inclusion的策略有区别）。
## 1.3Eviction
如果从LLC中复制了相应的块到上一级cache中，那么上一级cache中的块根据相应的替换策略进行块的替换或者叫驱逐。
# 2.cache locality
代码中的一部分指令常常会被经常使用，重复执行
```
for (i=0, i<N, i++){
  for (j=0, j<M, j++){
      ... Loop Body ...
  }     
}
```
其中的Loop Body称为热点，在一段时间会被不断执行，称为时间局部性。
时间局部性：如果一个块被访问了一定次数，接下来很有可能还会被访问。
空间局部性：如果一个块被访问了一定次数，那么很有可能其周围的块也会被访问。
# 3.cache 
![](/assets/images/1.png)

上面这个图是cache的概述。
## 3.1替换策略
**Random**：随即替换cache，可看出对性能的影响十分大。
**LRU**：需要每次访问进行更新；完全的LRU策略只能在small associates上实行（i.e 2 way）
； pseudo-LRU一般用于较大的associate（4-8 way）
**FIFO**：highly associate cache
**MRU NMRU**
## 3.2写策略
**cache hit**

**write through**：修改cache中块的值，并同时修改主存中相应块的值。

**write back**：修改 Cache 中的 Block 的数值后将此 Block 标记为 Dirty Block，但并不会立即修改主存中相对应的 Block 的数值，而是等到 Cache 中的此 Block 被驱逐時 (Eviction)，才会修改主存中相对应的 Block 的数值。此方式有数据丢失的危险，但是速度较快。

**cache miss**

**write allocate**：

將主存中含有想要写入的 Block 复制到 CPU Cache 中，并再修改 Cache 中该 Block 的数值后将该 Block 标记为 Dirty Block。

**write no allocate**：

仅仅是写到主存中，不会被写回到cache中。

**common combination**：

write through & write no allocate   
write back & write allocate
## 3.3cacheline解释
![](/assets/images/Image.png)

上图是cache的组织方式，cache由set组成，set由line组成，cacheline由valid bit，tag，data组成，其中data是真正要缓存的内存地址中的数据，而tag是用来搜索cache line的标签。
内存地址分解如下：
![](/assets/images/3.png)

内存地址被分成了3部分，tag，set index和block offset，这三部分分别用来在cache中定位数据。
cache根据寻址方式可以分为3类：
* 直接映射（direct mapped cache），相当于每个set只有1个cache line。
* 组相联（set associate cache）：多个set，每个set中有n个cacheline，就称为n-way set cache
* 全相连，即只有一个set

![](/assets/images/4.png)

Tag 是用来区分不同的 Block 的标记。Valid Bit 是用来区分 一个 Block 里的资料是否为有效的。
Valid -> 1  Invalid -> 0  
Cache 的大小的计算公式为：<a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;C=S\times&space;E\times&space;B" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\inline&space;C=S\times&space;E\times&space;B" title="C=S\times E\times B" /></a> data bytes

<a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;S=2^{s}" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\inline&space;S=2^{s}" title="S=2^{s}" /></a> sets

<a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;E=2^{e}" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\inline&space;E=2^{e}" title="E=2^{e}" /></a> lines per set

<a href="https://www.codecogs.com/eqnedit.php?latex=\inline&space;B=2^{b}" target="_blank"><img src="https://latex.codecogs.com/svg.latex?\inline&space;B=2^{b}" title="B=2^{b}" /></a> bytes per cache block (data)

# 4.Inclusion&Exclusion
![](/assets/images/5.png)

其中inclusive cache包含上一级的cache，因此如果由上一级cache的clean写回操作，直接复制就好，不需要写操作，对于exclusive不包含上一级cache的副本，因此不能避免dirty eviction。如果发生LLC miss，inclusive cache需要对LLC和L2同时进行write allocate，保证inclusive，对于exclusive则不需要。
# 5.cache miss(3C)

**compulsory miss(cold cache)**
* 程序刚开始执行时，cache中没有任何的块，因此发生了cache miss

**capacity miss**
* cache的容量太小了，没有办法覆盖到整个working set

**conflict miss**
* 虽然 Cache 还有很多空间，但在 Working Set 中的 任意两个 Block 可能无法同时存在 Cache 中。发生的原因可能是因为，此 2 个 Block 的 Index 相同。(也就是所谓的 Cache Thrash)

