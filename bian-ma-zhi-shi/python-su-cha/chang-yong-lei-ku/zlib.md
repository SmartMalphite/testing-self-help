# zlib

### python3

```text
>>> import zlib
>>> a = "123456789"
>>> zlib.crc32(a.encode("ascii"))
3421780262
```

### python2

```text
>>> import zlib
>>> import ctypes
>>> a = "123456789"
>>> zlib.crc32(a.encode("ascii"))
-873187034
>>> ctypes.c_uint(-873187034)
c_uint(3421780262L)
```



