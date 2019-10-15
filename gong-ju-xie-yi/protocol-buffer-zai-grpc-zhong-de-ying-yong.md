# Protocol Buffer在gRPC中的应用

## 相关库的安装

```text
$ python -m pip install grpcio
$ python -m pip install grpcio-tools googleapis-common-protos
```

## Demo程序功能概述

> 服务器端存在Test\_service类中定义了my\_function方法，客户端通过gRPC协议进行远程调用；该方法实现的功能是将接受到的字符串内容全部改为大写并返回

## PB接口描述文件定义

```python
syntax = "proto3";
package data;

#定义数据结构
message Data{
    string text=1;
}

#定义服务接口，即声明我要调用目标服务器上的哪个函数
service Test_service{
    #定义调用函数名称、参数类型与返回数据类型
    rpc my_function(Data) returns (Data) {}
}
```

## 编译

```text
python3 -m grpc_tools.protoc -I. --python_out=. --grpc_python_out=. ./data.proto
```

## 客户端代码

```python
#! /usr/bin/env python
# -*- coding: utf-8 -*-
import grpc
import data_pb2, data_pb2_grpc

_HOST = '127.0.0.1'
_PORT = '8080'

def run():
    conn = grpc.insecure_channel(_HOST + ':' + _PORT)
    client = data_pb2_grpc.Test_serviceStub(channel=conn)
    response = client.my_function(data_pb2.Data(text='hello,world!'))
    print("received: " + response.text)

if __name__ == '__main__':
    run()
```

## 服务器代码

```python
#! /usr/bin/env python
# -*- coding: utf-8 -*-
import grpc
import time
from concurrent import futures
import data_pb2, data_pb2_grpc

_ONE_DAY_IN_SECONDS = 60 * 60 * 24
_HOST = '127.0.0.1'
_PORT = '8080'

class Test_service(data_pb2_grpc.Test_serviceServicer):
    def my_function(self, request, context):
        str = request.text
        return data_pb2.Data(text=str.upper())

def serve():
    grpcServer = grpc.server(futures.ThreadPoolExecutor(max_workers=4))
    data_pb2_grpc.add_Test_serviceServicer_to_server(Test_service(), grpcServer)
    grpcServer.add_insecure_port(_HOST + ':' + _PORT)
    grpcServer.start()
    try:
        while True:
            time.sleep(_ONE_DAY_IN_SECONDS)
    except KeyboardInterrupt:
        grpcServer.stop(0)

if __name__ == '__main__':
    serve()
```

## 客户端运行结果

```python
$ python3 client.py 
received: HELLO,WORLD!
```

远程方法调用成功！

