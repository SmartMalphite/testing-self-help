# Apache支持web.py\(wsgi\)

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

