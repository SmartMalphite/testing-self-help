### 通过pip方式安装
安装方法如下：
```
$ pip install qtaf
```
> 如果是mac os的用户，在使用pip安装的时候最好指定--user选项，否则安装的script脚本可能无法使用。


### 通过脚手架创建项目
在安装好QTAF后，可以在终端中执行一下命令:
```
$ qta-manage createproject test
```
如果执行qta-manage提示找不到对应命令，则也可以通过以下方式执行:
```
$ python -m qta-manage createproject footestproj
```
执行成功后，可以看到当前目录下生成一下结构的文件:
```
/testproj/
            /testlib/
                   /__init__.py
                   /testcase.py
            /testtest/
                    /__init__.py
                    /hello.py
            /.project
            /.pydevproject
            /settings.py
            /manage.py
```