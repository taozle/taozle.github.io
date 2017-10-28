---
layout: post
title: "字节序谬论"
date: 2017-10-3
categories: Weekly
tags: ByteOrder
---

当看到代码判断 native 字节序的时候，基本上代码写错了或者被误导了。如果 native 字节序真的对程序的执行流程产生影响，那还是代码错了，或者被误导了。如果你代码里面有 `ifdef BIG_ENDIAN` 这种东西，那么你应该抛弃字节序。

机器的字节序对除了需要关心内存的分配布局到寄存器（例如编译器作者），其他情况都不应该关心。如果你不写 编译器，你就不应该关心机器的字节序。

Little Endian unsigned 32 bit

```c
i = (data[0]<<0) | (data[1]<<8) | (data[2]<<16) | (data[3]<<24);
```

Big Endian unsigned 32 bit

```c
i = (data[3]<<0) | (data[2]<<8) | (data[1]<<16) | (data[0]<<24);
```

这种代码在任何机器上都可以工作，和机器字节序无关。


```c
i = *((int*)data);
#ifdef BIG_ENDIAN
/* swap the bytes */
i = ((i&0xFF)<<24) | (((i>>8)&0xFF)<<16) | (((i>>16)&0xFF)<<8) | (((i>>24)&0xFF)<<0);
#endif
```

这种写法不好，为什么？

1. 代码多
2. 它假定了 int 在任何位置都是可以取地址的，但是这个假设并不是一定成立的
3. 它假定了 int 一定是 32 bit 的
4. 可能在 little endian 机器上快一点，在 big endian 上会慢
5. 你在 little endian 的机器上写这段代码，那么你没办法测试 big endian 的代码（为什么？）
6. swap the bytes，麻烦的标志

对比之前的代码

1. 短
2. 没有对齐的问题
3. 计算 32 bit 的 int 而不用管 local 的 int 的大小
4. 非常快
5. 在任何平台都可以工作
6. 没有 byte swap

简单来说：简单、干净、可移植



写了 byte swap 的代码就说明写的人完全不明白字节序是怎么工作的。



### 参考

https://commandcenter.blogspot.hk/2012/04/byte-order-fallacy.html
