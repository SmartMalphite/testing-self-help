# QTAF

## 1.安装

### 通过pip方式安装

安装方法如下：

```text
$ pip install qtaf
```

> 如果是mac os的用户，在使用pip安装的时候最好指定--user选项，否则安装的script脚本可能无法使用。

### 通过脚手架创建项目

在安装好QTAF后，可以在终端中执行一下命令:

```text
$ qta-manage createproject test
```

如果执行qta-manage提示找不到对应命令，则也可以通过以下方式执行:

```text
$ python -m qta-manage createproject footestproj
```

执行成功后，可以看到当前目录下生成一下结构的文件:

```text
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

## 2.Demo用例

### 一个基本的测试用例

下面我们来编写第一个QTA测试用例，在测试项目中新建一个hello.py:

```python
from testbase.testcase import TestCase

class HelloTest(TestCase):
    '''第一个测试用例
    '''
    owner = "foo"
    status = TestCase.EnumStatus.Ready
    priority = TestCase.EnumPriority.Normal
    timeout = 1

    def run_test(self):
        #---------------------------
        self.start_step("第一个测试步骤")
        #---------------------------
        self.log_info("hello")

if __name__ == '__main__':
   HelloTest().debug_run()
```

可以看到，这个最简单的测试用例包括以下主要的部分：

> 一个测试用例就是一个Python类，类的名称就是测试用例名，类的DocString就是测试用例的简要说明文档。**注意DocString对于QTA测试用例而言是必要的，否则会导致执行用例失败**。

测试用例类包括四个必要的属性：

> owner，测试用例负责人，必要属性。 status，测试用例状态，必要属性。目前测试用例有五种状态：Design、Implement、Review、Ready、Suspend。 priority，测试用例优先级，必要属性。目前测试用例有四种优先级：BVT、High、Normal和Low。 timeout，测试用例超时时间，必要属性，单位为分钟。超时时间用于指定测试用例执行的最长时间，如果测试用例执行超过此时间，执行器会停止用例执行，并认为用例执行超时，测试不通过。一般来说，不建议这个时间设置得太长，如果用例需要比较长的执行时间，可以考虑拆分为多个测试用例。

run\_test函数：

> 这个是测试逻辑的代码，每个测试用例只有一个唯一的run\_test函数；但每个测试用例可以划分为多个测试步骤，测试步骤以start\_step函数调用来分隔。

以上的测试用例并没有任何测试逻辑，只是调用接口打印一行日志。

注意，测试步骤之间的区分是按照 **self.start\_step\("xxx"\)** 进行区分的



## 3.测试用例标签

测试用例除了owner、timeout、status和priority之外，还有一个自定义的标签属性“tags”。测试标签的作用是，在批量执行用例的时候，用来指定或排除对应的测试用例

```python
from testbase.testcase import TestCase

class HelloTest(TestCase):
    '''第一个测试用例
    '''
    owner = "foo"
    status = TestCase.EnumStatus.Ready
    priority = TestCase.EnumPriority.Normal
    timeout = 1
    tags = "Demo"

    def run_test(self):
        #---------------------------
        self.start_step("第一个测试步骤")
        #---------------------------
        self.log_info("hello")
```

标签支持一个或多个，下面的例子也是正确的:

```python
from testbase.testcase import TestCase

class HelloTest(TestCase):
    '''第一个测试用例
    '''
    owner = "foo"
    status = TestCase.EnumStatus.Ready
    priority = TestCase.EnumPriority.Normal
    timeout = 1
    tags = "Demo", "Help"

    def run_test(self):
        #---------------------------
        self.start_step("第一个测试步骤")
        #---------------------------
        self.log_info("hello")
```

需要注意的是，测试用例标签经过框架处理后，会变成set类型，比如上面的用例:

> assert HelloTest.tags == set\("Demo", "Help"\)

## 4.测试环境初始化和清理

在前面的例子中，我们在测试用例类的run\_test实现了测试的主要逻辑，这里我们引入两个新的接口pre\_test和post\_test。

假设我们的用例需要临时配置一个本地host域名，示例代码如下:

```python
from testbase.testcase import TestCase

class EnvTest1(TestCase):
    '''环境构造测试
    '''
    owner = "foo"
    status = TestCase.EnumStatus.Ready
    priority = TestCase.EnumPriority.Normal
    timeout = 1

    def run_test(self):

        _add_host("www.qq.com", "11.11.12.12")

        # main test logic here
        # ...

        _del_host("www.qq.com", "11.11.12.12")
```

以上的代码在逻辑，在用例正常执行完成的情况下是完全正确的，但是这里存在一个问题，就是当run\_test测试过程中，由于测试目标bug或者脚本问题导致run\_test异常终止，则可能导致host配置没有删除，则可能影响到后面的测试用例。如何解决这个问题呢？QTA为此提供了post\_test接口。

```python
from testbase.testcase import TestCase

class EnvTest3(TestCase):
    '''环境构造测试
    '''
    owner = "foo"
    status = TestCase.EnumStatus.Ready
    priority = TestCase.EnumPriority.Normal
    timeout = 1

    def pre_test(self):
        super(EnvTest3, self).pre_test()
        _add_host("www.qq.com", "11.11.12.12")

    def run_test(self):
        # main test logic
        # ...
        pass

    def post_test(self):
        super(EnvTest3, self).post_test()
        _del_host("www.qq.com", "11.11.12.12")
```

#### 如何复用前置与后置条件

```python
from testbase.testcase import TestCase

class EnvTestBase(TestCase):

    def pre_test(self):
        super(EnvTestBase, self).post_test()
        _add_host("www.qq.com", "11.11.12.12")

    def post_test(self):
        super(EnvTestBase, self).post_test()
        _del_host("www.qq.com", "11.11.12.12")

class EnvTest4(EnvTestBase):
    '''环境构造测试
    '''
    owner = "foo"
    status = TestCase.EnumStatus.Ready
    priority = TestCase.EnumPriority.Normal
    timeout = 1

    def run_test(self):
        # code 1
        pass
```



## 5.如何判断测试步骤是否成功

> qtaf的新版本，提供了新的断言函数assert\_，推荐使用新接口编写用例

```python
from testbase.testcase import TestCase

class StrCombineTest(TestCase):
    '''测试字符串拼接接口
    '''
    owner = "foo"
    status = TestCase.EnumStatus.Ready
    priority = TestCase.EnumPriority.Normal
    timeout = 1

    def run_test(self):
        #---------------------------
        self.start_step("测试字符串拼接")
        #---------------------------
        result = string_combine("xxX", "yy")
        self.assert_("检查string_combine调用结果", result == "xxXyy")
```

### 正常校验通过

```text
============================================================
测试用例:StrCombineTest 所有者:enbowang 优先级:Normal 超时:1分钟 
============================================================

----------------------------------------
步骤1: 测试字符串拼接
============================================================
测试用例开始时间: 2016-02-02 14:10:21
测试用例结束时间: 2016-02-02 14:10:21
测试用例执行时间: 00:00:0.00
测试用例步骤结果:  1:通过
测试用例最终结果: 通过
============================================================
```

### 出现异常，检验失败

```text
============================================================
测试用例:StrCombineTest 所有者:foo 优先级:Normal 超时:1分钟
============================================================
----------------------------------------
步骤1: 测试字符串拼接
ASSERT: 检查点不通过:
  File "D:\foo\Workspace\DemoProj\test_assert.py", line 22, in run_test
    self.assert_("检查string_combine调用结果", result == "xxXyy")
 [检查string_combine调用结果] assert  'xxXb' == 'xxXyy'
============================================================
测试用例开始时间: 2018-08-27 17:00:30
测试用例结束时间: 2018-08-27 17:00:31
测试用例执行时间: 00:00:0.06
测试用例步骤结果:  1:失败
测试用例最终结果: 失败
============================================================
```

> 注意，由于python的断言机制，所以用例除了出现异常活超时，测试用例会一直执行，即使断言失败，也会继续执行；对于断言失败的执行逻辑处理，这个是QTA测试框架和其他一般测试框架比较大的差异点，设计测试用例是需要注意。

## 6.数据驱动测试用例

先看一个简单的例子，小E需要设计一个针对登录的测试用例：

输入“111”登录QQ，登录失败，提示非法帐号 输入“”登录QQ，登录失败，提示非法帐号 输入“11111111111111111”登录QQ，登录失败，提示非法帐号 输入“$%^&\#”登录QQ，登录失败，提示非法帐号 如果需要实现对应的自动化测试用例，则可能会写类似下面的代码:

```text
from testbase.testcase import TestCase

class InvalidUinTest1(TestCase):
    '''非法测试号码1
    '''
    owner = "foo"
    status = TestCase.EnumStatus.Ready
    priority = TestCase.EnumPriority.Normal
    timeout = 1

    def run_test(self):
        qq = QQApp()
        login = LoginPanel(qq)
        login['uin'] = "111"
        login['passwd'] = "test123"
        login['login'].click()
        self.assertEqual(login['tips'].text, "非法帐号")

class InvalidUinTest2(TestCase):
    '''非法测试号码2
    '''
    owner = "foo"
    status = TestCase.EnumStatus.Ready
    priority = TestCase.EnumPriority.Normal
    timeout = 1

    def run_test(self):
        qq = QQApp()
        login = LoginPanel(qq)
        login['uin'] = ""
        login['passwd'] = "test123"
        login['login'].click()
        self.assertEqual(login['tips'].text, "非法帐号")


class InvalidUinTest3(TestCase):
    '''非法测试号码3
    '''
    owner = "foo"
    status = TestCase.EnumStatus.Ready
    priority = TestCase.EnumPriority.Normal
    timeout = 1

    def run_test(self):
        qq = QQApp()
        login = LoginPanel(qq)
        login['uin'] = "11111111111111111"
        login['passwd'] = "test123"
        login['login'].click()
        self.assertEqual(login['tips'].text, "非法帐号")

class InvalidUinTest4(TestCase):
    '''非法测试号码4
    '''
    owner = "foo"
    status = TestCase.EnumStatus.Ready
    priority = TestCase.EnumPriority.Normal
    timeout = 1

    def run_test(self):
        qq = QQApp()
        login = LoginPanel(qq)
        login['uin'] = "$%^&#"
        login['passwd'] = "test123"
        login['login'].click()
        self.assertEqual(login['tips'].text, "非法帐号")

if __name__ == '__main__':
   InvalidUinTest1().debug_run()
   InvalidUinTest2().debug_run()
   InvalidUinTest3().debug_run()
   InvalidUinTest4().debug_run()
```

从上面的代码看出，用户的逻辑基本上是类似的，每个用例几乎只有一点点的差异，特别是如果测试的场景变多了，用例维护起来更麻烦。

这里我们就可以用数据驱动用例来解决这个问题，使用数据驱动修改后的用例:

```text
from testbase.testcase import TestCase
from testbase import datadrive

testdata = [
  "111",
  "",
  "11111111111111111",
  "$%^&#",
]

@datadrive.DataDrive(testdata)
class InvalidUinTest(TestCase):
    '''非法测试号码
    '''
    owner = "foo"
    status = TestCase.EnumStatus.Ready
    priority = TestCase.EnumPriority.Normal
    timeout = 1

    def run_test(self):
        qq = QQApp()
        login = LoginPanel(qq)
        login['uin'] = self.casedata
        login['passwd'] = "test123"
        login['login'].click()
        self.assertEqual(login['tips'].text, "非法帐号")

if __name__ == '__main__':
    InvalidUinTest().debug_run()
```

如果执行以上的代码，其输出的结果是和前面四个测试用例的执行结果是一致的，但是这里却只有一个用例，这个就是数据驱动测试用例的强大之处。

上面的数据驱动测试用例和一般测试用例的主要区别在两点：

测试用例类增加了修饰器“testbase.datadrive.DataDrive”，修饰器接受一个参数来指定对应的测试数据 测试用例通过casedata属性获取测试数据

### 测试数据类型

#### list类型测试数据

```text
@datadrive.DataDrive(["AA", 1234234, {"xx":"XX"},  True])
class HelloDataTest(TestCase):
   pass
```

则生成的四个测试用例对应的casedata分别为:

```text
"AA"

1234234

{"xx":"XX"}

True
```

#### dict类型测试数据

```text
@datadrive.DataDrive({
   "A": "AA",
   "B": 1234234,
   "C": {"xx":"XX"},
   "D": True
})
class HelloDataTest(TestCase):
   pass
```

则生成的四个测试用例对应的casedata分别为:

```text
"AA"

1234234

{"xx":"XX"}

True
```

但dict的键在这里似乎没什么用处？ 数据驱动用例的调试时，可以和一般用例一样使用debug\_run接口，例如:

```text
if __name__ == '__main__':
   HelloDataTest().debug_run()
```

使用debug\_run调试时，会执行全部数据驱动的用例，如果需要针对单个数据进行调试，可以使用debug\_run\_one接口:

```text
if __name__ == '__main__':
   HelloDataTest().debug_run_one()
```

