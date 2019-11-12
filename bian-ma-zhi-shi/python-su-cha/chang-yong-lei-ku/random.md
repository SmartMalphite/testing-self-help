# random

```text
>>> import random
>>> import string

#随机整数
>>> random.randint(1,50)
44

#随机浮点数
>>> random.random()
0.7033550976952957
>>> random.uniform(1, 10)
5.795832333341846

#随机偶数(能被2整除，如果换成则是能被3整除)
>>> random.randrange(0, 101, 2)
60
>>> random.randrange(0, 101, 3)
63

#随机选择
>>> random.choice('abcdefghijklmnopqrstuvwxyz!@#$%^&*()')
'x'
>>> random.choice(['剪刀', '石头', '布'])
'布'

#多个字符中生成指定数量的随机字符,可以通过join拼接
>>> random.sample('zyxwvutsrqponmlkjihgfedcba',5)
['h', 'l', 'm', 'q', 'n']
>>> ''.join(random.sample('zyxwvutsrqponmlkjihgfedcba',5))
'rweaq'

#从a-zA-Z0-9生成指定数量的随机字符
>>> ''.join(random.sample(string.ascii_letters + string.digits, 8))
'I0P6Rmfu'

#打乱排序
>>> items = [1, 2, 3, 4, 5, 6, 7, 8, 9, 0]
>>> random.shuffle(items)
>>> items
[0, 9, 4, 7, 8, 1, 6, 2, 3, 5]
```

