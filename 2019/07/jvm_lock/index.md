# JVM锁


### 线程安全
#### 多线程网站统计访问人数
- 使用锁, 维护计数器的串行访问与安全性
- 多线程方位ArrayList
```java
public static List<Integer> numberList = new ArrayList<Integer>();
public static class AddToList implements Runnable{
    int startnum=0;

    public AddToList(int startnumber){
        startnum=startnumber;
    }

    @Override
    public void run(){
        int count=0;
        while(count<1000000){
            numberList.add(startnum);
            startnum+=2;
            count++;
        }
    }
}

public static void main(String[] args) throws InterruptedException {
    Thread t1 = new Thread(new AddToList(0));
    Thread t2 = new Thread(new AddToList(1));
    t1.start();
    t2.start();
    while(t1.isAlive()||t2.isAlive()){
        Thread.sleep(1);
    }
    System.out.println(numberList.size());
}
```

### 对象头Mark
- Mark Word , 对象头的标记, 32位
- 描述对象的hash, 锁信息, 垃圾回收标记, 年龄
    - 指向锁记录的指针
    - 指向monitor的治身
    - GC标记
    - 偏向锁线程ID

### 偏向锁
- 大部分情况是没有竞争的, 所以可以通过偏向来提高性能
- 所谓偏向, 就是偏心, 即锁会偏向于当前已经占有锁的线程
- 将对象头Mark标记设置为偏向, 并将线程ID写入对象头Mark
- 只要没有竞争, 获得偏向锁的线程,在将来进入同步块,不需要做同步
- 当其他线程请求相同的锁时, 偏向模式结束
- -XX:+UseBiasedLocking 默认启用
- 在竞争激烈的场合, 偏向锁会增加系统负担

```java
// -XX:+UseBiasedLocking -XX:BiasedLockingStartupDelay=0
public static List<Integer> numberList = new Vector<Integer>();
public static voi main(String[] args) throws InterruptedException {
    long begin = System.currentTimeMills();
    int count = 0;
    int startnum = 0;
    while(count< 10000000){
        numberLis.add(startnum);
        startnum+=2
        count++;
    }
    long end = System.currentTimeMillis();
    System.out.println(end-begin);
}
```

### 轻量级锁
- BasicObjectLock
- 嵌在线程栈中的对象
{{<mermaid>}}
graph LR
  A(BasicObjectLock)-->B(BasicLock)
  B-->C(markOop_displaced_header)
  A-->D(ptr to obj hold the lock)
{{</mermaid>}}


- 普通锁的处理性能不够理想,轻量级锁是一种快速锁定的方法
- 如果对象没有被锁定
1. 将对象头的Mark指针保存到锁对象
2. 将对象头设置为指向锁的指针(在线程栈空间中)
```java
lock->set_displaced_header(mark);
if(mark== (markOop)Atomic::cmpxchg_ptr(lock,obj()->mark_addr(),mark)){
    TEVENT(slow_enter: release stacklock);
    return;
}
```
- 如果轻量级锁失败,表示存在竞争,升级为重量级锁(常规锁)
- 在没有锁竞争的前提下,减少传统锁使用OS互斥产生的性能损耗
- 在竞争激烈时,轻量级锁会做很多额外操作,导致性能下降

### 自旋锁
- 当竞争存在时, 如果香橙可以很快获得锁, 那么可以不在OS层挂起线程, 让线程做几个空操作(自旋)
- JDK 1.6 中通过 -XX:+UseSpinning开启
- JDK 1.7中去掉此参数, 内置默认开启
- 如果同步块很长,自旋失败,会降低系统性能
- 如果同步块很短,自旋成功, 节省线程挂起切换时间,提升系统性能

### 偏向锁,轻量级锁,自旋锁总结
- 不是Java语言层面的锁优化方案
- 内置于JVM中的获取锁的优化方法和获取锁的步骤
1. 偏向锁可用会现场时偏向锁
2. 轻量级锁可用会先尝试轻量级锁
3. 以上都失败,尝试自旋锁
4. 再失败,尝试普通锁,使用OS互斥量操作系统层挂起

### java语言层面优化
#### 减少锁持有时间
```java
//修改前
public synchronized void syncMethod(){
    othercode1();
    mutextMethod();
    othercode2();
}

//修改后
public void syncMethod2(){
    othercode1();
    synchronized(this){
        mutextMethod();
    }
    othercode2();
}
```

#### 减小锁粒度
- 将大对象,拆成小对象,大大增加并行度,降低锁竞争
- 偏向锁, 轻量级锁成功率提高
- ConcurrentHashMap
- HashMap的同步实现
1. Collections.synchronizedMap(Map<K,V> m)
2. 返回SynchronizedMap对象
```java
public V get(Object key){
    synchronized(mutext){
        return m.get(key);
    }
}
public V pug(K key, V value){
    synchronized(mutext){
        return m.put(key,value);
    }
}
```
- ConcurrentHashMap
1. 分成若干个Segment: Segment<K,V> [] segments
2. Segment中维护HashEntry<K,V>
3. put操作时: 先定位到Segment, 锁定一个Segment, 执行put
- 在减小锁粒度后, ConcurrentHashMap允许若干个线程同时进入


#### 锁分离
- 根据功能进行锁分离
- ReadWriteLock
- 读多写少的情况,可以提高性能

略|读锁|写锁|
-|-|-
读锁|可访问|不可访问
写锁|不可访问|不可访问

- 读写分离思想可以延伸, 只要操作互不影响,锁就可以分离
- LinkedBlockingQueue
1. 队列
2. 链表 

### 锁粗化
- 通常情况下, 为了保证多线程间的有效并发,会要求每个线程持有锁的时间尽量短,即在使用完公共资源后,应该立即释放锁.只有这样,等待在这个锁上的其他线程才能尽早获得资源执行任务. 但是,凡事都有一个度,如果对同一个锁不停的进行请求,同步和释放, 其本身也会消耗系统宝贵资源,反而不利于性能的优化
```java
// 优化前
public void demoMethod(){
    synchronized(lock){
        //do sth.
    }
    //做其他不需要的同步工作,但能很快执行完毕
    synchronized(lock){
        //do sth.
    }
}

//优化后
public void demoMethod(){
    //整合成一次锁请求
    synchronized(lock){
        //do sth
        //do other
    }
}
```

### 锁消除
- 在即时编译器时,如果发现不可能被共享的对象,则可以消除这些对象的锁操作
```java
// CIRCLE=2000000
// -server -XX:+DoEscapeAnalysis -XX:+EliminateLocks 187ms
// -server -XX:+DoEscapeAnalysis -XX:-EliminateLocks 254ms
public static void main(String args[]) throws InterruptedException {
    long start = System.currentTimeMillis();
    for(int i = 0;i<CIRCLE;i++){
        createStringBuffer("JVM","Diagnosis");
    }
    long bufferCost = System.currentTimeMillis();
    System.out.println("createStringBuffer:" + bufferCost + " ms");
}

public static String createStringBuffer(String s1, String s2){
    StringBuffer sb = new StringBuffer();
    sb.append(s1); // append 方法内置synchronize锁,但是这里是失效的
    sb.append(s2);
    return sb.toString();
}
```

### 无锁
- 锁是悲观的操作
- 无锁是乐观的操作
- 无锁的一种实现方式
1. CAS(Compare And Swap)
2. 非阻塞的同步
3. CAS(Valve,Expect,NewValue)
- 在应用层面判断多线程的干扰,如果有干扰,则通知线程重试
- java.util.concurrent.atomic.AtomicInteger
```java
public final int getAndSet(int new Value){
    for(;;){
        int current = get();
        if(compareAndSet(current,newValue))
            return current;
    }
}
```



