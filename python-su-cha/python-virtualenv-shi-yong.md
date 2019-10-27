# Python virtualenv 使用

#### 安装

> $ pip3 install virtualenv

#### 使用

第一步，创建目录：

```text
Mac:~ $ mkdir myproject
Mac:~ $ cd myproject/
Mac:myproject $
```

第二步，创建一个独立的Python运行环境，命名为venv：

```text
Mac:myproject $ virtualenv --no-site-packages venv -p /usr/local/bin/python3
Using base prefix
'/usr/local/.../Python.framework/Versions/3.4'
New python executable in venv/bin/python3.4
Also creating executable in venv/bin/python
Installing setuptools, pip, wheel...done.
```

命令virtualenv就可以创建一个独立的Python运行环境，我们还加上了参数--no-site-packages，这样，已经安装到系统Python环境中的所有第三方包都不会复制过来，这样，我们就得到了一个不带任何第三方包的“干净”的Python运行环境。

第三步,新建的Python环境被放到当前目录下的venv目录。有了venv这个Python环境，可以用source进入该环境：

```text
Mac:myproject $ source venv/bin/activate
(venv)Mac:myproject $
```

第四步,退出当前的venv环境，使用deactivate命令

```text
(venv)Mac:myproject $ deactivate 
Mac:myproject $
```

