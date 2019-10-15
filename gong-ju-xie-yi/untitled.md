# Protocol Buffer基本使用

## 前言

> 由于最近需要对gRPC进行使用，所以要首先了解一下PB相关的相关知识。这里为大家梳理一下相关的知识点与Python版本的实践。 给大家推荐一个很详细的学习地址链接,如果需要进行深入学习[点击这里](https://www.cntofu.com/book/116/index.html)

## 简介

> Protocol Buffers\(以下简称pb\)，是google开发的一个可以序列化 反序列化object的数据交换格式，类似于xml，但是比xml 更轻，更快，更简单。而且以上的重点突出一个跨平台，和xml json等数据序列化一样，跨平台跨语言。 是一个语言无关，平台无关，可扩展的结构化数据序列化方案, 用于协议通讯, 数据存储和其他更多用途.

## 安装

```text
1.github:https://github.com/google/protobuf/releases 下载最新版本的对应语言的pb
2.下载完毕之后运行包中自带configure文件。
3.make&&make install。
4.进入python文件夹 
5.运行安装命令
>  $ python setup.py build
>  $ python setup.py test
```

## PB描述文件定义

> PB的使用需要定义一个.proto文件，该文件里面会定义号数据类型和格式。我在这里就不再搬运各语言对应的字段，因为这些官方文档中都写的非常清楚。这里直接最典型的使用方法。

```text
syntax = "proto3";

package people;

message Person {
    string name = 1;
    int32 id = 2;
    enum Gender {     //定义枚举类型，每个枚举定义必须包含一个映射到0的常量作为它的第一个元素
        FEMALE = 0;
        MALE = 1;
    }
    Gender gender = 3;

    enum PhoneType {    //定义枚举类型，每个枚举定义必须包含一个映射到0的常量作为它的第一个元素
        MOBILE = 0;
        HOME = 1;
        WORK = 2;
    }

    message PhoneNumber {     //定义嵌套消息类型
        string number = 1;
        PhoneType type = 2;
    }
    repeated PhoneNumber phones = 4;      //动态数组，构成元素的数据类型是上面定义的PhoneNumber
}
```

## 编译

```text
Python命令：
protoc -I=$SRC_DIR --python_out=$DST_DIR $SRC_DIR/people.proto
生成一个people_pb2.py文件

C++命令：
protoc -I=$SRC_DIR --cpp_out=$DST_DIR $SRC_DIR/ people.proto
生成people.pb.h和people.pb.cc文件

Java命令：
protoc -I=$SRC_DIR --java_out=$DST_DIR $SRC_DIR/ people.proto
每个message生成一个.java文件
```

## Python+PB实例

```python
from test_pb2 import Person

person = Person()

person.name = "xx.xxx"
person.id = 123456
person.gender = 1

#数组+嵌套类型的使用，元素1添加
phone1 = person.phones.add()
phone1.number = "654321"
phone1.type = 2

#数组+嵌套类型的使用，元素2添加
phone2 = person.phones.add()
phone2.number = "123456"
phone2.type = 1

#序列化
result = person.SerializeToString()

print(person)
print(result)

#反序列化
person = Person()
person.ParseFromString(result)
print(person)
```

运行结果如下

```python
$ python3 main.py 

#序列化前的结果
name: "xx.xxx"
id: 123456
gender: MALE
phones {
  number: "654321"
  type: WORK
}
phones {
  number: "123456"
  type: HOME
}

#序列化后的数据
b'\n\x06xx.xxx\x10\xc0\xc4\x07\x18\x01"\n\n\x06654321\x10\x02"\n\n\x06123456\x10\x01'

#反序列化后的数据
name: "xx.xxx"
id: 123456
gender: MALE
phones {
  number: "654321"
  type: WORK
}
phones {
  number: "123456"
  type: HOME
}
```

