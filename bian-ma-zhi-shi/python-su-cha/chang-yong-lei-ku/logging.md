---
description: 通用日志模块
---

# logging

## 1.基础使用

```text
#!/usr/local/bin/python3
# -*- coding:utf-8 -*-
import logging

logging.debug('debug message')
logging.info('info message')
logging.warning('warn message')
logging.error('error message')
logging.critical('critical message')
```

输出结果：

```text
WARNING:root:warn message
ERROR:root:error message
CRITICAL:root:critical message
```

结论：  
 \*. 默认 打印等级：WARNING\(即只有日志级别高于WARNING的日志信息才会输出\)  
 \*. 默认 打印格式：`[%(levelname)s]:[%(name)s]:[%(message)s]` 即：`[日志等级]:[Logger实例名称]:[日志消息内容]`  


## 2.核心概念

> **1.Logger 记录器**：暴露了应用程序代码能直接使用的接口。
>
>  **2.Handler 处理器**：将（记录器产生的）日志记录发送至合适的目的地。
>
>  **3.Filter 过滤器**：提供了更好的粒度控制，它可以决定输出哪些日志记录。
>
>  **4.Formatter 格式化器**：指明了最终输出中日志记录的布局。

### 2.1 Logger 记录器

> Logger是一个树形层级结构，在使用接口debug，info，warn，error，critical之前必须创建Logger实例，即创建一个记录器，如果没有显式的进行创建，则默认创建一个root logger，并应用默认的日志级别\(WARN\)，处理器Handler\(StreamHandler，即将日志信息打印输出在标准输出上\)，和格式化器Formatter\(默认的格式即为第一个简单使用程序中输出的格式\)。

```text
import logging
#logger 创建
logger = logging.getLogger(logger_name)
```

创建Logger实例后，可以使用以下方法进行日志级别设置，增加处理器Handler。

```text
logger.setLevel(logging.ERROR)  # 设置日志级别为ERROR，即只有日志级别大于等于ERROR的日志才会输出
logger.addHandler(handler_name)  # 为Logger实例增加一个处理器
logger.removeHandler(handler_name)   # 为Logger实例删除一个处理器
```

### 2.2 常见Handler 处理器

#### 2.2.1 **StreamHandler**

能够将日志信息输出到sys.stdout, sys.stderr 或者类文件对象（更确切点，就是能够支持write\(\)和flush\(\)方法的对象）

```text
sh = logging.StreamHandler(stream=None)
#日志信息会输出到指定的stream中，如果stream为空则默认输出到sys.stderr
```

#### **2.2.2 FileHandler**

继承自StreamHandler。将日志信息输出到磁盘文件上

```text
sh = logging.FileHandler(filename, mode='a', encoding=None, delay=False)
模式默认为append，delay为true时，文件直到emit方法被执行才会打开。默认情况下，日志文件可以无限增大。
```

#### 2.2.3 **RotatingFileHandler**

```text
sh = logging.handlers.RotatingFileHandler(filename, mode='a', maxBytes=0, backupCount=0, encoding=None, delay=0)

参数maxBytes和backupCount允许日志文件在达到maxBytes时rollover.当文件大小达到或者超过maxBytes时，就会新创建一个日志文件。
上述的这两个参数任一一个为0时，rollover都不会发生。也就是就文件没有maxBytes限制。backupcount是备份数目，也就是最多能有多少个备份。
命名会在日志的base_name后面加上.0-.n的后缀，如example.log.1,example.log.1,…,example.log.10。当前使用的日志文件为base_name.log。
```

#### 2.2.4 **TimedRotatingFileHandler**

```text
sh = logging.handlers.TimedRotatingFileHandler(filename, when='h', interval=1, backupCount=0, encoding=None, delay=False, utc=False)

参数when决定了时间间隔的类型，参数interval决定了多少的时间间隔。如when='D'，interval=2，就是指两天的时间间隔，backupCount决定了能留几个日志文件。超过数量就会丢弃掉老的日志文件。

when的参数决定了时间间隔的类型。两者之间的关系如下：
 'S'         |  秒
 'M'         |  分
 'H'         |  时
 'D'         |  天
 'W0'-'W6'   |  周一至周日
 'midnight'  |  每天的凌晨
utc参数表示UTC时间。
```

### 2.3 Formatter 格式化器

使用Formatter对象设置日志信息最后的规则、结构和内容，默认的时间格式为%Y-%m-%d %H:%M:%S。

```text
创建方法: 
formatter = logging.Formatter(fmt=None, datefmt=None, style='%')
fmt：消息的格式化字符串，如果不指明fmt，将使用'%(message)s'。
datefmt：日期字符串。如果不指明datefmt，将使用ISO8601日期格式。
style：引用样式，默认'%', 即'%()s'
```

### 2.4 Filter 过滤器

Handlers和Loggers可以使用Filters来完成比级别更复杂的过滤。Filter基类只允许特定Logger层次以下的事件。

例如用'A.B'初始化的Filter允许Logger 'A.B', 'A.B.C', 'A.B.C.D', 'A.B.D'等记录的事件，logger'A.BB', 'B.A.B' 等就不行。如果用空字符串来初始化，所有的事件都接受。

```text
创建方法: filter = logging.Filter(name='')
```

## 3.自定义配置

### 3.1 **format 格式中的变量**

| **格式** | 描述 |
| :--- | :--- |
| %\(asctime\)s | 打印日志的时间 |
| %\(process\)d | 打印进程ID |
| %\(thread\)d | 打印线程id |
| %\(threadName\)s | 打印线程名称 |
| %\(levelname\)s | 打印日志级别名称 |
| %\(levelno\)s | 打印日志级别的数值 |
| %\(pathname\)s | 打印当前执行程序的路径 |
| %\(filename\)s | 打印当前执行程序名称 |
| %\(funcName\)s | 打印日志的当前函数 |
| %\(lineno\)d | 打印日志的当前行号 |
| %\(message\)s | 打印日志信息 |

