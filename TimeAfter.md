# TimeAfter

#### select与timeafter

理解了select的运行机制的话就会发现，如果select中有多个time.after时，则等于只有时间最短的那个会被触发，其他都无效。

#### timeafter与time.tick

两种写法如果都是一次性使用看起来没有区别（实际完全不同），但在循环中如果需要反复重置定时器，应该使用timeafter，否则等于每次循环体中都初始化一个定时器，最终占满CPU。