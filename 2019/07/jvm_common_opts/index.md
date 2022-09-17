# JVM常用运行参数


### Trace跟踪参数

- -verbose:gc
- -XX:+PrintGC
- 可以打印GC的简要信息
- -XX:+PrintGCDetails 打印GC详细信息
- -XX:+PrintGCTimeStamps 打印GC发生的时间戳
- -Xloggc:log/gc.log 指定GC log的位置,以文件输出, 帮助开发人员分析问题
- -XX:+PrintHeapAtGC 每一次GC后,都打印堆信息
- -XX:+TraceClassLoading 监控类的加载
- -XX:+PrintClassHistogram 按下Ctrl+Break后, 打印类的信息. 分别显示的是: 序号, 实例数量, 总大小, 类型

### 堆的分配参数

- -Xmx 最大堆
- -Xms 最小堆

```java
System.out.print("Xmx=");
System.out.println(Runtime.getRuntime().maxMemory()/1024/1024 + "M");

System.out.print("free mem=");
System.out.println(Runtime.getRuntime().freeMemory()/1024/1024 + "M");

System.out.print("total mem=");
System.out.println(Runtime.getRuntime().totalMemory()/1024/1024 + "M");
```
- -Xmn 设置新生代大小,是一个绝对值
- -XX:NewRatio 按比例设置新生代的比例: 新生代(eden + 2*s)和老年代(不包含永久区)的比值. 例如: 4表示 新生代:老年代=1:4, 即年轻代占堆的1/5
- -XX:SurvivorRatio 设置两个Survivor区和eden的比. 例如: 8表示 两个Survivor:eden = 2:8, 即一个Survivor占年轻代的1/10

```java
/*
通过设置不同的JVM参数观察GC的情况: 合理减少幸存代大小提高GC效率, 减少GC
-Xmx20m -Xms20m -Xmn1m -XX:+PrintGCDetail 不会触发GC,全部分配在老年代
-Xmx20m -Xms20m -Xmn15m -XX:+PrintGCDetails 不会触发GC,全部分配在eden,老年代没有使用
-Xmx20m -Xms20m -Xmn7m -XX:+PrintGCDetails 进行了2次新生代GC, s0,s1太小需要老年代担保
-Xmx20m -Xms20m -Xmn7m -XX:SurvivorRatio=2 -XX:+PrintGCDetails 进行了3次新生代GC, s0,s1增大
-Xmx20m -Xms20m -XX:NewRatio=1 -XX:SurvivorRatio=2-XX:+PrintGCDetails
*/

public static void main(String[] args){
    byte[] b= null;
    for(int i=0;i<10;i++){
        b = new byte[1*1024*1024];
    }
}
```

- -XX:+HeapDumpOnOutOfMemoryError OOM时导出堆到文件
- -XX:+HeapDumpPath 导出OOM的路径

```java
/*
-Xmx20m -Xms5m -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=d:/a.dump
*/
Vector v = new Vector();
for(int i =0;i<25;i++){
    v.add(new byte[1*1024*1024]);
}
```

- -XX:OnOutOfMemoryError 在OOM的时候, 执行一个脚本.
```shell
-XX:OnOutOfMemoryError=D:/tools/jdk1.7_40/bin/printstatck.bat %p # 打印线程信息, 这个脚本可以是发送邮件甚至是重启程序
```
官方推荐新生代占堆的3/8, 幸存代占新生代的1/10. OOM时,记得Dump出堆, 确保可以排查现场问题

### 永久区分配参数

- -XX:PermSize -XX:MaxPermSize 设置永久区的初始控件大小和最大空间大小. 他们表示一个系统可以容纳多少个类型
```java
/*
使用CGLIB等库的时候,可能会产生大量的类. 这些类有可能承包永久区导致OOM
*/
for(int i=0;i<100000;i++){
    CglibBean bean = new CglibBean("geym.jvm.ch3.perm.bean"+ i, new HashMap());
}
```
- 以上表示: 堆空间实际占用非常少, 但是永久区溢出,一样会出现OOM的情况

### 栈的分配参数
- -Xss
1. 通常只有几百k
2. 决定了函数的深度
3. 每个线程有独立的栈空间
4. 局部变量,参数分配在栈上
