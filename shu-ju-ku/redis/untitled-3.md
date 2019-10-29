# 4-Redis数据结构-有序集合

### redis数据结构 – 有序集合

> redis不但提供了无需集合（sets），还很体贴的提供了有序集合（sorted sets）。有序集合中的每个元素都关联一个序号（score），这便是排序的依据。

很多时候，我们都将redis中的有序集合叫做zsets，这是因为在redis中，有序集合相关的操作指令都是以z开头的，比如zrange、zadd、zrevrange、zrangebyscore等等

老规矩，我们来看几个生动的例子： //新增一个有序集合myzset，并加入一个元素baidu.com，给它赋予的序号是1：

```text
127.0.0.1:6379> zadd myzset 1 baidu.com 
(integer) 1
//向myzset中新增一个元素360.com，赋予它的序号是3
127.0.0.1:6379> zadd myzset 3 360.com 
(integer) 1
//向myzset中新增一个元素google.com，赋予它的序号是2
127.0.0.1:6379> zadd myzset 2 google.com 
(integer) 1
//列出myzset的所有元素，同时列出其序号，可以看出myzset已经是有序的了。
127.0.0.1:6379> zrange myzset 0 -1 with scores 
1) "baidu.com"
2) "1"
3) "google.com"
4) "2"
5) "360.com"
6) "3"
//只列出myzset的元素
127.0.0.1:6379> zrange myzset 0 -1 
1) "baidu.com"
2) "google.com"
3) "360.com"
```

