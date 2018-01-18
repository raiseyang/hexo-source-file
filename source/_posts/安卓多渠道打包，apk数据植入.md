title: 安卓多渠道打包，apk数据植入
author: raiseyang
tags: []
categories:
  - android
date: 2017-10-21 18:08:00
---
# 安卓多渠道打包，apk数据植入


---
## 调研
安卓多渠道打包主要为了解决apk入口渠道问题；为了统计各个应用市场等apk入口，也有分成等原因；
目前主流的技术方案有4种(直接github搜索[渠道])：
1. apktool 拆包，再修改数据，再重新打包。[友盟方案](https://github.com/umeng/umeng-muti-channel-build-tool)
2. 在apk非签名区域MATE-INF增加文件的方式。[AndroidMultiChannelBuildTool](https://github.com/GavinCT/AndroidMultiChannelBuildTool)
3. 利用的是Zip文件“comment（摘要）”区域，写入信息。[MultiChannelPackageTool](https://github.com/seven456/MultiChannelPackageTool)
4. Android Signature V2 Scheme签名下的新一代渠道包打包神器[美团点评walle](https://github.com/Meituan-Dianping/walle)
<!-- more -->
| 方案        | V1签名   |  V2签名  |先写后签| 先签后写| 打包速度
| --------   | :-----:  | :----:  |
| 1        | √ |        |√ ||慢
| 2        |   √   |      ||√|快
| 3        |    √    |    ||√|快
| 4        |        |  √  ||√|快

## 问题&方案
而我们公司有这样一种使用场景需要植入信息到apk中:
1. 客户在我们后台创建一个项目;
2. 该项目下会动态生成项目的token;
3. 客户将token值给到研发人员;
4. 研发人员再编译成新apk,提供给客户;
5. 客户再使用签名对该apk再次签名;

该过程繁琐,低效.其中1,2,3,5步骤都是客户完成,我们研发人员仅做第4步,但是却无形的增加了沟通成本.
> 所有我们就想有没有一种方案,我们出一个植入token的工具;该工具只需要输入一个token值,就可以生成植入token值之后的apk.
当客户按照以前的业务场景进行到第4步时,他可以直接运行该工具,得到想要的apk.然后再自己签名.

这种解决方案和多渠道打包很像,都是想apk动态植入数据,但是又有些不一样,因为必须先植入再签名.通过上面的调研,基本可以确定只能使用apktool这种方案.

## 实现
github上的友盟项目已经不维护,况且apktool技术本身也并不复杂,所以可以自己实现;
那么首先需要找到[apktool官网](https://ibotpeaches.github.io/Apktool/),正好官网上有详细的对apk做拆包,组包的文档.那么我们就依靠官方文档实现一下几部即可.
1. 编译apk,得到目标文件`raise.apk`(使用as编译最新版本即可)
2. 使用`apktool`工具,将`raise.apk`拆包
3. 使用`java`或`python`脚本修改`Androidmanifest.xml`文件,注入token值
4. 使用`apktool`工具,生成`raise_new.apk`

apktool使用到的命令:
```
apktool d raise.apk

apktool b raise
```