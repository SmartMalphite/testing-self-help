# web.py\(无模板\)

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

