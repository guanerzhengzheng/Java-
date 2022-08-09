## 1、JVM相关

### 1.1 理论知识 

#### 1.1.1 内存模型

##### 1)方法区

- 定义
  - 方法区是抽象的概念，永久代和元数据区是方法区的实现 
- 存储的内容 
  - 类型信息, 方法信息，字段信息
  - 静态变量  -- 1.7后放到了堆区
  - 运行时常量池 --class 字节码中的常量池加载而来，每个class都对应自己的运行时常量池  - 1.7后放到了堆区
    - 字面量类型 
      - 字符串常量
    - 符号引用类型
  - JIT 编译后的代码缓存   字节码编译成机器指令 
- 永久代和元空间的区别 
  -  jdk 1.8之前  方法区的实现是永久代 之后是元空间
  -  存储位置不同 永久代是JVm 进程使用的空间，元空间使用的是物理内存
  -  存在的内容不同， 元空间只存储类的元 数据 
- 为啥要替换永久代 
  - 字符串存在永久代，容易出现内存性能问题和内存溢出
  - 大小比较难指定
  - 给gc增加复杂度

- 备注：类的类型信息从class 文件读取的，class 文件中包括了很大一块的常量池， 主要存储依赖类的指针

#### 1.1.2 类加载

1）加载时机 

- 按需加载

2）加载过程

- 加载，加载class文件 
- 验证，验证class文件是否符合规范 
- 准备，  static 变量初始化 为null 或者 0 
- 解析，把符号引用翻译为直接引用
- 初始化 ，new 对象，访问和修改类的静态属性，反射都会触发类的初始化，这时静态变量会进行初始化
- 使用
- 卸载
  - 所有实例都被回收
  - 加载该类的classLoader 也被回收 
  - 该class 对象没有被引用

3）类加载器 

- 启动类加载器
  - 加载 jre/lib下的jar包 
- 扩展类加载器
  - 加载jre/lib/ext 下的jar包
-  应用程序加载器
  - 加载用户classpath下的所有class 

4）加载模式  -双亲委派模型

- 检查类是否被加载，自下而上

- 加载类的使用的类加载器的顺序，自上而下 

  <img src="/Users/zhengyonggang/Desktop/MyFile/博客/JVM相关/image-20220724165104761.png" alt="image-20220724165104761" style="zoom: 50%;" align="left" />

- 双亲委派模型设计的原因 

  - 避免Java 核心类被加载
  - 避免重复加载
  -  确保类的唯一性性 

5）自定义类加载器

- 继承ClassLoad 
- 覆盖 findClass方法 不会打破 双亲委派
- 或者load class 方法    会打破双亲委派

6）ClassLoad中loadClass  findClass defineClass的区别 

- loadClass() 就是主要进行类加载的方法，其中实现了双亲委派算法
- findClass 根据名称或者位置加载class字节码 
- defineClass将字节码转化为Class 

7）class.forName 和 class.loadClass的区别

- class.forName会初始化类 
-  class.loadClass 只是加载了类，不会对类进行初始化 

#### 1.1.3 JVM 中对象内存的分布 

1) 对象的组成

- 对象头
  - 对象自身运行时的数据  哈希码，GC分代年龄，锁状态标志 ，线程持有的锁， 线程线程ID，偏向时间戳
  - 类型指针，指向元空间的class的元数据 
- 实例数据 
- 对其填充

#### 1.1.4 垃圾回收机制

1） 堆的分区

- 年轻代   占比 1/3    Edia 和 survivor的占比8:1:1 
-  年老代  占比 2/3 

2）动态年龄判断

- survivor区的对象从小到大进行累加，当累加到X年龄时占用空间的总和大于 50%，那么比X年龄大的对象会晋升到老年代

3)  对象进入老年代的时间节点 

-  躲过了15次major gc

- 动态年龄判断  

- 老年代空间担保机制

  <img src="/Users/zhengyonggang/Library/Application Support/typora-user-images/image-20220726022341856.png" alt="image-20220726022341856" style="zoom:50%;" align = "left"/>

- 大对象直接进入老年代  

  - PretenuureSizeThreahold 设置多大的字节直接进入老年代 



#### 1.1.5 JVM 相关的重要参数

- Xms 初始堆内存大小 
- Xmx  堆的最大值 
- Xmn 堆中年轻代的大小
- MatespaceSize  元空间大小
- MaxMetaspaceSize 最大元空间大小
- Xss   每个线程的栈内存大小
- SuvivorRatio = 8 edina 和 survivor区的比例大小是8:1:1
-  MaxTenuringThreshold  =15(并发收集器) 年龄阈值 
-  MaxTenuringThreshold  =6(CMS收集器)

#### 1.1.6 如何实现Java热部署 

- 重写classLoad 
- 定时任务 
- 根据修改时间判断class是否发生变化

#### 1.1.7  Java中不同的引用类型 

- 强引用   - 内存不足时会报 out of memery 
- 软引用  -  内存不足时会被回收
- 弱引用   - 只要gc内存就会被回收 
- 虚引用  





#### 1.1.4 垃圾收集器 

##### 1）CMS （Concurent Mark Sweet）   -标记清除算法

- 定义
  - 最短回收停顿时间为目标的收集器 
- 收集步骤  
  - 初始标记  -标记gc root 直接关联的对象 ， 速度很快，会触发stop world
  - 并发标记  -并发进行剩余对象的标记 ，用户进程也会并发的执行任务，不会stop the world，但是比较耗时
  - 重新标记  -因为并发标记的用户进程还在执行，所以对象的依赖关系可能发送变化，需要重新标记，此时会stop the world
  - 并发清除 - 用户进程会并发执行，不会stop the world 
-  优点 
  - 停顿时间段 
- 缺点 
  - 产生大量的空间碎片 

##### 2) G1 垃圾收集器  - 标记整理算法 

- 特点 
  - 将堆内存 分为2048 个 region 区域，有逻辑上的各种分区
  - <img src="/Users/zhengyonggang/Desktop/MyFile/博客/JVM相关/image-20220723224509264.png" alt="image-20220723224509264" style="zoom:33%;" align="left"/>
  - Hum 大对象 超过region大小的一半
  - 优先回收垃圾最多的region，在有限的时间内尽量回收最多垃圾
  - 可以设置垃圾回收预期的停顿时间 
  - 新生代达到堆的 60% 开始回收，会stw 
  - 新生代默认 占堆的 5%   最大60% 动态变化
  - 回收过程
    - 初始标记  -标记gc root 直接关联的对象 ， 速度很快，会触发stop world
    - 并发标记  -并发进行剩余对象的标记 ，用户进程也会并发的执行任务，不会stop the world，但是比较耗时
    - 重新标记  -因为并发标记的用户进程还在执行，所以对象的依赖关系可能发送变化，需要重新标记，此时会stop the world
    - 并发清除 - 用户进程会并发执行，不会stop the world 
  - 混合垃圾回收 
  - fullgc  对象的分配赶不上对象回收的速度
  - ![image-20220808034048665](/Users/zhengyonggang/Library/Application Support/typora-user-images/image-20220808034048665.png)

##### 3）Serial 垃圾收集器 

- 新生代，串行，单线程  client 模型下的垃圾收集器 

##### 4）ParNew

- 新生代的垃圾收集器 ，多线程 ，尽量配置成和cpu 核数一样

5）Parallel 

- 新生代 并行多线程，复制算法，达到可控的吞吐量，新生代默认的垃圾收集器  



#### 1.1.5 内存泄露 



1.1.6 线上参数配置

![image-20220808034646339](/Users/zhengyonggang/Library/Application Support/typora-user-images/image-20220808034646339.png)



### 1.2 线上问题结合

1.1 线上参数配置 





