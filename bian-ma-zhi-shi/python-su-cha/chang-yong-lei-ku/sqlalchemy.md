---
description: python 数据库o'r'm
---

# sqlalchemy

## python SQLAlchemy自动生成models文件

1.安装SQLAcodegen  

```text
pip install sqlacodegen
```

2.执行

```text
sqlacodegen mysql://root:123456@127.0.0.1:3306/test > models.py
#会在当前目录下生成models.py
```

3.如果是python3  会报错 No module named 'MySQLdb'

这个时候安装pymysql。

 然后在sqlacodegen 的\_\_init\_\_.py文件里加上

```text
import pymysql
pymysql.install_as_MySQLdb()
#再次执行，成功
```

