# JVM运行机制

### JVM运行机制

#### PC寄存器

- 每个线程拥有一个PC寄存器
- 在线程创建时创建
- 总是指向下一条指令的地址
- 执行本地方法时, PC的值为undefined

#### 方法区

- 保存装载的类信息
  - 类型的常量池
  - 字段, 方法信息
  - 方法字节码
- 通常和永久区关联在一起

#### java堆

- 和程序开发密切相关
- 应用系统对象都保存在java堆中
- 所有线程共享java堆
- 对分代GC来说, 堆也是分代
- GC的主要工作区间: eden|s0|s1|tenured

#### java栈

- 线程私有
- 栈由一系列帧组成(因此java栈也叫做栈帧)
- 帧保存一个方法的局部变量,操作数栈,常量池指针
- 每一次方法调用创建一个帧,并压栈

#### java栈之局部变量表(包含参数和局部变量)

```java
public class StackDemo {
  /* 假设局部变量表里是一个槽位, 每个槽位最大容纳32位的数据*/

  /* 静态方法的局部变量表为:
       0 int int i
       1 long long l
       2 float float f
       3 reference Object o
       4 int byte b
  */
  public static int run Static(int i, long l, float f, Object o, byte b){
    return 0;
  }

    /* 实例方法的局部变量表为:
       0 reference this 当前对象的引用
       1 int char c
       2 int short s
       3 int boolean bµ
    */
  public int runInstance(char c, short s, boolean b){
    return 0;
  }
}
```

#### java栈之函数调用组成帧栈(包括操作数栈和返回地址)

```java
/*
函数调用过程, 会压栈
*/
public static int runStatic(int i, long l, float f, Object o, byte b){
	return runStatic(i, l, f, o, b);
}
```

#### java栈之操作数栈

```java
/*
  java没有寄存器, 所有参数传递使用操作数栈
  以下方法的操作数栈情况:
  0 iconst_0 0压栈
  1 istore_2 弹出int,存放于局部变量2
  2 iload_0 把局部变量0压栈
  3 iload_1 把局部变量1压栈
  4 iadd 弹出2个变量,求和,结果压栈
  5 istore_2 弹出结果,放于局部变量2
  6 iload_2 局部变量2压栈
  7 ireturn 返回
*/
public static int add(int a, int b){
    int c = 0;
    c = a + b;
    return c;
}
```

#### java栈之栈上分配

```java
class BcmBasicString{...}

/*
堆上分配, 每次要手动清理内存
*/
public void method(){
  BcmBasicString* str = new BcmBasicString;
  ...
  delete str;
}

/*
栈上分配, 不会出现内存泄漏
*/
public void method(){
  BcmBasicString str;
  ...
}
```

栈上分配特点

- 小对象(一般几十个bytes), 在没有逃逸的情况下, 可以直接分配在栈上
- 直接分配在栈上, 可以自动回收, 减轻GC压力
- 大对象或者逃逸对象(即会被共享的对象)无法栈上分配

#### 堆, 栈, 方法区交互

```java
public class AppMain{
  //运行时, jvm把appmain的信息都放入方法区
  public static void main(String[] args){
    //test1是引用, 放到栈区, Sample是自定义对象, 放到堆里面
    Sample test1 = new Sample("test1");
    Sample test2 = new Sample("test2");
    test1.printName();
    test2.printName();
  }
}


public class Sample{
  //运行时, jvm把sample的信息都放入方法区
  // new Sample实例后, name引用放到栈区里, name对象放到堆里
  private String name;

  public Sample(String name){
    this.name = name;
  }

  // print 方法本身放入方法区里
  public void printName(){
    System.out.println(name);
  }

}
```

#### 内存模型

- 每一个线程有一个工作从内存和主存独立
- 工作内存存放主存中变量值的拷贝
- 当数据从"主内存"复制到"线程工作内存"时, 必须出现两个动作:

1. 主内存执行的(read)操作
2. 工作内存执行相应的load操作

- 当数据从"线程工作内存"拷贝到"主内存"时, 也出现两个动作:

1. 工作内存执行store操作
2. 主内存执行相应的写write操作

- 每个操作都是原子的, 即执行期间不会被中断
- 对于普通变量, 一个线程中更新的值,不能马上反应在其他变量中. 如果需要在其他线程中立即课件, 需要使用volatile关键字

#### volatile

```java
/*
  volatile 不能替代锁, 一般认为volatile比锁性能好(不绝对)
  选择使用volatile的条件是: 语义是否满足应用, 它是线程不安全的
*/
public class VolatileStopThread extends Thread{
  private volatile boolean stop = false;
  public void stopMe(){
    stop = true;
  }

  public void run(){
    int i = 0;
    while(!stop){
      i++;
    }
    System.out.println("Stop thread");
  }

  public static void main(String[] args) throws InterruptedException{
    VolatileStopThread t = new VolatileStopThread();
    t.start();
    Thread.sleep(1000);
    t.stopMe();
    Thread.sleep(1000);
  }
}
```

#### 可见性

- 一个线程修改了变量, 其他线程可以立即知道

#### 保证可见性的方法

- volatile
- synchronized(unlock之前, 写变量值回主存)
- final(一旦初始化完成,其它线程就可见)

#### 有序性

- 在本线程内,操作都是有序的
- 在线程外观察,操作都是无序的(指令重排 或 主内存同步延时)

#### 指令重排的基本原则

- 程序顺序原则: 一个线程内保证语义的串行性
- volatile规则: volatile变量的写, 先发生于读
- 锁规则: 解锁(unlock)必然发生在随后的加锁(lock)前
- 传递性: A先于B, B先于C 那么A必然先于C
- 线程的start方法先于它的每一个动作
- 线程的所有操作先于线程的终结(Thread.join())
- 线程的中断(interrupte())先于被中断线程的代码
- 对象的构造函数执行先于finalize()方法

#### 解释执行

- 解释执行以解释方式运行字节码
- 解释执行的意思是: 读一句执行一句

#### 编译运行(JIT)

- 将字节码编译成机器码
- 直接执行机器码
- 运行时编译
- 编译后性能有数量级的提升
