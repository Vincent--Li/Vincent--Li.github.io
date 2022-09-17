# Java GC算法与种类


### GC的概念
- java中,GC的对象是堆空间和永久区

### GC算法

#### 引用计数法 没有被java采用
通过引用计数来回收垃圾, 但是有问题:
- 引用和去引用伴随加法和减法,影响性能
- 很难处理循环引用的问题

#### 标记清除
现代垃圾回收算法的思想基础. 将垃圾回收分为两个阶段:
- 标记阶段  
在标记阶段,首先通过根节点, 标记所有从根节点开始的可达对象. 因此,未被标记的对象就是未被引用的垃圾对象
- 清除阶段
清除所有未被标记的对象

#### 标记压缩
适用于存活对象比较多的场合. 在标记-清除算法的基础上做了一些优化. 首先要从根节点开始,对所有可达对象做一次标记. 但之后不是简单的清理未标记的对象,而是将所有的存活对象压缩到内存的一端. 之后清理边界外所有空间

#### 复制算法
- 与标记-清除算法相比, 复制算法是一种相对高效的回收算法
- 不适用于存活对象较多的长河, 如老年代
- 将原有的内存空间分为两块. 每次只使用其中一块, 在垃圾回收时, 将正在使用的内存中的存活对象复制到未使用额内存块中, 之后, 清除正在使用的内存块中的所有对象, 交换两个内存的角色, 完成垃圾回收
- 最大的问题是: 空间浪费, 整合标记清理思想

#### 分代思想
- 根据对象的存活周期进行分类, 短命对象归为新生代, 长命对象归为老年代
- 根据不同代的特点, 选取合适的回收算法
1. 少量对象存活,适合复制算法
2. 大量对象存活,适合标记清理或者标记压缩

### 可触及性
所的算法, 需要能够识别一个垃圾对象, 因此需要给出一个可触及性的定义
- 可触及的: 从根节点可以触及到这个对象
- 可复活的: 一旦所有引用被释放,就是可复活状态, 因为在finalize()中可能复活该对象
- 不可触及的: 在finalize()后,可能会进入不可触及状态, 不可触及的对象不可能复活, 可以回收
```java
/*
finalize方法只会被调用一次
*/
public class CanReliveObj{
    public static CanReliveObj obj;

    @Override
    protected void finalize() throws Throwable {
        super.finalize();
        System.out.println("CanReliveObj finize called");
        obj = this;
    }

    @Override
    public String toString(){
        return "I am CanReliveObj";
    }
}

public static void main(String[] args) throws InterruptedException{
    obj = new CarReliveObj();
    obj = null; //可复活
    System.gc();
    Thread.sleep(1000);
    if(obj==null){
        System.out.println("obj is null");
    }else{
        System.out.println("obj is no null");
    }
    System.out.println("第二次GC");
    obj = null; //不可复活
    System.gc();
    Thread.sleep(1000);
    if(obj==null){
        System.out.println("obj is null");
    }else{
        System.out.println("obj is no null");
    }
}
```

- 经验: 避免使用finalize(), 操作不慎可能导致错误
- 优先级低, 何时被调用, 不确定. 因为GC不确定
- 使用try-catch-finally替代它
- 根:
1. 栈中引用的对象
2. 方法区中静态成员或者常量引用的对象(全局对象)
3. JNI方法栈中引用对象

### Stop-The-World

- JAVA中一种全局暂停的现象
- 全局停顿, 所有Java代码停止, native代码可以执行, 但不能和JVM交互
- 多半由GC引起(其它的情况如下)  
1. dump线程
2. 死锁检查
3. 堆dump
- 危害  
1. 长时间服务停止,没有响应
2. 遇到HA系统,可能引起主备切换,严重危害生产环境
