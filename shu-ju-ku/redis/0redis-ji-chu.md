# 0-Redis基础

#### 进入本地命令行客户端

```text
EnboWangdeMacBook-Pro:~ enbowang$ redis-cli
127.0.0.1:6379>
```

#### 远程连接redis客户端

```text
$ redis-cli -h host -p port -a password
```

#### Redis键 操作

```text
127.0.0.1:6379> set test 123
OK

127.0.0.1:6379> get test
"123"

127.0.0.1:6379> del test
(integer) 1
```

![](../../.gitbook/assets/image%20%2810%29.png)



![](../../.gitbook/assets/image%20%283%29.png)

参考链接 [https://www.cnblogs.com/jianzhixuan/p/6427640.html](https://www.cnblogs.com/jianzhixuan/p/6427640.html)

