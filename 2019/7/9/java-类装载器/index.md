# Java 类装载器


### class装载验证流程
#### 加载
- 装载类的第一阶段
- 取得类的二进制流
- 转为方法区数据结构
- 在Java堆中生成对应的java.lang.Class对象

#### 链接
##### 验证
目的: 保证Class流的格式是正确的
- 文件格式的验证
1. 持否以0xCAFEBABE开头
2. 版本号时候合理
- 元数据验证
1. 是否有父类
2. 继承了final类?
3. 非抽象类是否实现了所有的抽象方法
- 字节码验证(很复杂)
1. 运行检查
2. 栈数据类型和操作码数据参数吻合
3. 跳转指令指定到合理的位置
- 符号引用验证
1. 常量池中描述类是否存在
2. 访问的方法或字段是否存在且有足够的权限

##### 准备
- 分配内存,并为类设置初始值(方法区中)
1. public static int v=1
2. 在准备阶段中, v会被设置为0
3. 在初始化的clinit中才会被设置为1
4. 对于static final类型, 在准备阶段就会被赋为正常的值
5. public static final int v=1

##### 解析
- 符号引用替换为直接引用
- 符号引用: 字符串引用对象不一定被加载
- 直接引用: 指针或者地址偏移量, 引用对象一定在内存

#### 初始化
- 执行类构造器clinit
1. static变量 赋值语句
2. static{}语句
- 子类的clinit调用前保证父类的clinit被调用
- clinit是线程安全的

### 什么是类装载器ClassLoader
- ClassLoader是一个抽象类
- ClassLoader的实例将读入Java字节码将类装载到JVM中
- ClassLoader可以定制,满足不同的字节码流获取方式
- ClassLoader负责类装在过程中的加载阶段

#### ClassLoader的重要方法
```java

//载入并返回一个Class
public Class<?> loadClass(String name) throws ClassNotFoundException

//定义一个类,不公开调用
protected final Class<?> defineClass(byte[] b, int off, int len)

//loadClass回调该方法,自定义ClassLoader的推荐方法
protected Class<?> findClass(String name) throws ClassNotFoundException

//寻找已经加载的类
protected final Class<?> findLoadedClass(String name)
```

### JDK中的ClassLoader默认设计模式
#### ClassLoader分类
- BootStrap ClassLoader(启动ClassLoader)
- Extension ClassLoader(扩展ClassLoader)
- App ClassLoader(应用ClassLoader/系统ClassLoader)
- Custom ClassLoader(自定义ClassLoader)
- 每个ClassLoader都有一个Parent作为父亲

#### ClassLoader的协同工作
1. 从Custom -> BootStrap 的顺序检查类是否已经加载
2. 从BootStrap -> Custom 尝试加载类
3. BootStrap默认加载rt.jar中的类, 我们可以使用 -Xbootclasspath指定加载类的外部路径
4. Extension默认加载 %JAVA_HOME%/lib/ext/*.jar
5. App 默认加载Classpath下的类

```java
// 在 -Xbootclasspath条件下, 强制加载某个类
public static void main(String[] args) throws Exception{
    ClassLoader cl = FindClassOrder2.class.getClassLoaer();
    byte[] bHelloLoaer = loadClassBytes("geym.jvm.ch6.findorder.HelloLoader");
    Method md_defineClass = ClassLoader.class.getDeclaredMethod("defineClass", byte[].class, int.class, int.class);
    md_defineClass.setAccessible(true);
    md_defineClass.invoke(cl, bHelloLoader, 0, bHelloLoader.length);
    md_defineClass.setAccessible(false);

    HelloLoader loader = new HelloLoader();
    System.out.println(loader.getClass().getClassLoader());
    loader.print();
}
```

#### 问题
- 双亲模式的问题: 顶层ClassLoader, 无法加载底层ClassLoader的类
- javax.xml.parser包中定义了xml解析类的接口Service Provider Interface SPI位于rt.jar. 接口在ClassLoader中. SPI实现类,在AppLoader

#### 解决
Thread.setContextClassLoader()
- 上下文加载器
- 是一个"角色"
- 用以解决顶层ClassLoader无法访问底层ClassLoader的问题
- 基本思想是,在顶层ClassLoader中,传入底层ClassLoader的实例

#### 双亲模式的破坏
- 双清模式是默认的模式,但是不是必须这么做
- Tomcat的WebappClassLoader就会先加载自己的Class, 找不到在委托parent
- OSGi的ClassLoader形成网状结构,根据需要自由加载Class

```java
//OrderClassLoader的实现. 破坏双亲模式,先从底层ClassLoader加载
protected synchronize Class<?> loadClass(String name, boolean resolve) throws ClassNotFoundException {
    //First, check if the class has already been loaded
    Class re = findClass(name);
    if(re==null){
        System.out.println("无法载入类:" + name + "需要请求父加载器");
        return super.loadClass(name, resolve);
    }
    return re;
}

protected Class<?> findClass(String className) throws ClassNotFoundException {
    Class clazz = this.findLoadedClass(className);
    if(null==clazz){
        try{
            String classFile = getClassFile(className);
            FileInputStream fis = new FileInputStream(classFile);
            FileChannel fileC = fis.getChannel();
            ByteArrayOutputStream baos = new ByteArrayOuntputStream();
            WritableByteChannel outC = Channels.newChannel(baos);
            ByteBuffer buffer = ByteBuffer.allocateirect(1024);
            ...
            fis.close();
            byte[] bytes = baos.toByteArray();

            clazz = defineClass(className, bytes, 0, bytes.length);
        }catch(FileNotFoundException e){
            e.printStackTrace();
        }catch(IOException e){
            e.printStackTrace();
        }
    }
    return clazz;
}
```

### 热替换

- 含义: 当一个class被替换后, 系统无需重启, 替换的类立即生效

```java
public class CVersionA {
    public void sayHello(){
        System.out.print("hello world! (version A)");
    }
}
```


