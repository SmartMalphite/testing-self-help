# WebPy

## 示例代码\(api,无模版\)

```python
# coding:utf-8
import web

urls = (
    '/.*', 'index',
    )

db = web.database(dbn='mysql', user='root', pw='web12138', db='entry_task')

"""
ErrorCode List:
0     查询成功
1001  系统错误
1002  name参数缺失
1003  price参数不合法（非数值或为负值）
1004  price参数缺失
1005  discount参数不合法（非数值或不在0-1之间）
1006  discount参数缺失
1007  数据查询失败
"""
class index:
    def GET(self):
        #原始数据获取
        try:
            data = web.input()
        except Exception as e:
            response = {
                "result":"1001",
                "error_message":"System Error",
                "reference":str(e).replace("'","")
            }
            return response
        #name参数解析
        try:
            name = data.name
        except Exception as e:
            response = {
                "result":"1002",
                "error_message":"No parameter name found!",
                "reference":str(e).replace("'","")
            }
            return response
        #price参数解析，合法性校验
        try:
            price = data.price
            #response = price
            try:
                price = float(price)
                #价格必须为正值
                if price<0:
                    raise RuntimeError("parameter price illegal!")
            except Exception as e:
                response = {
                    "result":"1003",
                    "error_message":"Parameter price is illegal!",
                    "reference":str(e).replace("'","")
                }
                return response
        except Exception as e:
            response = {
                "result":"1004",
                "error_message":"No parameter price found!",
                "reference":str(e).replace("'","")
            }
            return response
        #discount参数解析，合法性校验
        try:
            discount = data.discount
            #response = discount
            try:
                discount = float(discount)
                #折扣必须为正值且介于0-1之间
                if discount<0 or discount>1:
                    raise RuntimeError("parameter discount illegal!")
            except Exception as e:
                response = {
                    "result":"1005",
                    "error_message":"Parameter discount is illegal!",
                    "reference":str(e).replace("'","")
                }
                return response
        except Exception as e:
            response = {
                "result":"1006",
                "error_message":"No parameter discount found!",
                "reference":str(e).replace("'","")
            }
            return response
        #实际价格计算
        price *= discount
        #数据库查询
        try:
            result = list(db.select("test",where="name='%s'"%(name)))
            account = result[0]["account"]
        except Exception as e:
            response = {
                "result":"1007",
                "error_message":"No data find in database!",
                "reference":str(e).replace("'","")
            }
            return response
        #判断能否购买，为了方便测试，这里更新后的余额就不写入数据库了
        if account >= price:
            buy = 1
            account = account-price
        else:
            buy = 0
        response = {
            "result":"0",
            "buy":"%s"%(buy),
            "account":"%s"%(account)
        }
        return response
application = web.application(urls, globals()).wsgifunc()
```

## Apache支持web.py\(wsgi\)

#### 解决方案

> 下载wsgi的安装包进行安装编译该so，过程中会提示缺少一些工具或命令
>
> ```text
> $ brew install apache解决
> ```
>
> 编辑 /usr/local/etc/httpd/httpd.conf 配置文件

```text
#将wsgi的module装载进apache
LoadModule wsgi_module lib/httpd/modules/mod_wsgi.so

#指定需要解析的脚本
WSGIScriptAlias /entrytask /usr/local/var/www/webapi/service.py/

AddType text/html .py
#目录权限控制
<Directory /usr/local/var/www/webapi/>
      Order deny,allow
      Allow from all
</Directory>
```

> 解决import库失败的问题 [参考链接](https://stackoverflow.com/questions/2232542/python-import-mysqldb-apache-internal-server-error)

问题原因是因为所找的路径范围不一样，需要将安装第三方库路径中的文件复制到webserver运行脚本时所找的路径中去

> web.py脚本更改 [参考链接](http://webpy.org/cookbook/mod_wsgi-apache.zh-cn)

样例如下：

```text
import web

urls = (
    '/.*', 'hello',
    )

class hello:
    def GET(self):
        return "Hello, world."

application = web.application(urls, globals()).wsgifunc()
```

