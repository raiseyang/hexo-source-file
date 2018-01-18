title: Kotlin不支持的语言特性
author: raiseyang
tags: []
categories:
  - kotlin
date: 2017-12-02 16:41:00
---
## 对象判断
```
val persion: Persion()
if(persion) //会报错
```

## 检查异常Checked Exception

## while()表达式的赋值操作
`while((len = is.read(buf))!=-1)`会报错

解决方式:is.read.apply {it = len} !=-1