title: python常见错误
author: raiseyang
tags:
  - python
categories:
  - python
date: 2017-08-05 19:11:00
---
#### TypeError: not enough arguments for format string
使用%s来格式化时，后面的参数需要传递一个tuple
```
>>> "%s %s" % 'hello', 'world'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: not enough arguments for format string
>>> "%s %s" % ('hello', 'world')
'hello world'
```
参考：https://stackoverflow.com/questions/11146190/python-typeerror-not-enough-arguments-for-format-string