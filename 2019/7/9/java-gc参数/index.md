# Java GC参数


### 堆回顾

### 串行收集器
- 最古老, 最稳定
- 效率高
- 可能会产生较长的停顿
- -XX:+UseSerialGC
1. 新生代,老年代使用串行回收
2. 新生代复制算法
3. 老年代标记-压缩

### 并行收集器
- ParNew
1. -XX:+UseParNewGC 新生代并行,老年代串行
2. Serial收集器新生代的并行版本
3. 复制算法
4. 多线程,需要多核支持
5. -XX:ParallelGCThreads 限制线程数量

- Parallel收集器
1. 类似ParNew
2. 新生代复制算法
3. 老年代 标记-压缩
4. 更加关注吞吐量
5. -XX:+UseParallelGC 使用Parallel收集器+老年代串行
6. -XX:+UseParallelOldGC 使用Parallel收集器+并行老年代

- -XX:MaxGCPauseMills 最大停顿时间, 单位毫秒 尽力保证回收时间不超过设定值
- -XX:GCTimeRatio 0-100取值范围, 垃圾收集时间占总时间的比,默认99即最大允许1%时间做GC
- 以上两个指标是矛盾的, 不能对停顿时间和吞吐量同时长调优

### CMS收集器
- Concurrent Mark Sweep 并发标记清除
- 标记-清除算法
- 与标记-压缩相比
- 并发阶段会降低吞吐量
- 老年代收集器(新生代使用ParNew)
- -XX:+UseConcMarkSweepGC
- CMS运行过程复杂, 着重实现了标记的过程, 可分为
1. 初始标记 根可以直接关联到对象, 速度快
2. 并发标记(和用户线程一起) 主要标记过程,标记全部对象
3. 重新标记 由于并发标记时,用户线程依然运行, 在正式清理之前,再做修正
4. 并发清除(和用户线程一起) 基于标记结果,直接清理对象
- 特点
1. 尽可能降低停顿
2. 会影响系统整体吞吐量和性能
3. 清理不彻底
4. 因为和用户线程一起执行,不能在空间快满时再清理
  - -XX:CMSInitiatingOccupancyFraction 设置触发GC的阈值
5. 使用串行收集器作为后备
- 有关碎片
1. 标记-清除 会产生碎片
2. 标记-压缩

- -XX:+UseCMSCompactAtFullCollection Full GC后,进行一次整理 整理过程是独占的,会引起停顿时间变长
- -XX:+CMSFullGCsBeforeCompaction 设置进行几次Full GC后,进行一次碎片整理
- -XX:ParallelCMSThreads 设置CMS的线程数量
- 减轻GC压力
1. 软件架构设计
2. 代码如何写
3. 堆空间的分配
