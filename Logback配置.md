### Logback 配置

#### 1、properties 属性 

 本质是K-V对，通Key和value

#### 2、appender

2.1 作用 ： logback中appender是负责写日志的组件，主要用于指定日志输出的目的地，目的地可以是控制台、文件、远程套接字服务器、MySQL和其他数据库、JMS和远程UNIX Syslog守护进程等 。
ConsoleAppender: 把日志打印到控制台
FileAppender: 把日志输出到文件
RollingFileAppender: 滚动记录地把日志输出到文件, 也就是可以支持日志文件按文件大小拆分,或者按日期拆分

#### 3、Logger和 root 标签

3.1Logger可以指定某个包下的类打印的日志等级和输出 appeneder，使用<appender-ref>指定
3.2 root 指定全局的包的打印的日志等级和输出 appeneder，
3.3 root 和 Logger 可以理解为全局和局部的关系， root 和 Logger 下的appeneder都会生效，但是 level 会以 Logger下的为准
3.4 root下level 建议配置为info

参考资料：

https://blog.csdn.net/qq_42861526/article/details/122842818