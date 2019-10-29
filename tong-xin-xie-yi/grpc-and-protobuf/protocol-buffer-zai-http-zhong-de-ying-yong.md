# Protobuf在HTTP中的应用

## 客户端代码

本例中分别演示了http+json的通信方式与http+Protocol Buffer的通信方式； 本例中的测试用例使用qtaf框架进行管理，实际应用可以视需求而定，只关注核心逻辑即可；

```text
# -*- coding: utf-8 -*-

from testbase.testcase import TestCase
from testbase import datadrive
from testbase.retry import Retry
import requests,json
import sys
from test_pb2 import Person

class Case001(TestCase):
    '''http_client
    '''
    owner = "enbowang"
    status = TestCase.EnumStatus.Ready
    priority = TestCase.EnumPriority.Normal
    timeout = 1

    #从这里开始进入核心逻辑
    def run_test(self):
        #json方式模拟
        self.start_step("http+json 请求测试")
        url = "http://127.0.0.1:8080/http_json"
        body = b'{"name":"xx.xxx"}'
        response = requests.post(url,data=body)
        self.log_info("body：" + str(body))
        self.log_info('响应状态：'+ str(response.status_code))
        self.log_info('响应内容：'+ str(response.text))

        #Protocol Buffer方式模拟，PB格式定义请见该系列上一篇文章
        self.start_step("http+Protocol Buffer 请求测试")
        url = "http://127.0.0.1:8080/http_proto"
        person = Person()
        person.name = "xx.xxx"
        person.id = 123456
        body = person.SerializeToString()
        response = requests.post(url,data=body)
        self.log_info("body：" + str(body))
        self.log_info('响应状态：'+ str(response.status_code))
        self.log_info('响应内容：'+ str(response.text))

if __name__ == '__main__':
    Case001().debug_run()
```

## 服务端代码

服务端使用webpy实现 分别实现了json数据的解析与PB数据的解析

```text
# coding:utf-8
import web,json
from test_pb2 import Person
urls = (
    '/http_json', 'index',
    '/http_proto','pb'
    )

#json请求进入该逻辑
class index:
    def GET(self):
        return "Hello"
    def POST(self):
        data = web.data()
        result = json.loads(data)
        return result['name']

#pb请求进入该逻辑
class pb:
    def GET(self):
        return "Hello"
    def POST(self):
        data = web.data()
        person = Person()
                    person.ParseFromString(data)    #反序列化
        return person.name

app = web.application(urls, globals())

if __name__ == "__main__":
    app.run()
```

## 客户端运行结果如下

![](../../.gitbook/assets/image%20%289%29.png)

