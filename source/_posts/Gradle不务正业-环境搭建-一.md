title: Gradle不务正业-环境搭建(一)
author: raiseyang
tags:
  - gradle
categories:
  - gradle
date: 2017-10-21 12:42:00
---
# Gradle不务正业-环境搭建(一)

标签（空格分隔）： Gradle

---

## 环境要求
JDK 1.7+

## 安装
1. 去[官方网站](https://gradle.org/releases/)下载最新版本,目前最新版本为`gradle-4.1-milestone-1`,下载地址:https://downloads.gradle.org/distributions/gradle-4.1-milestone-1.
2. 解压到`D:`目录下.
3. 配置`GRADLE_HOME`环境变量为:`D:\gradle-4.1-milestone-1`;并将`%GRADLE_HOME%\bin`添加到PATH中.
3. 打开命令行窗口,输入下例命令验证gradle配置.
<!-- more -->
```
PS D:\gradle-4.1-milestone-1> gradle -v

------------------------------------------------------------
Gradle 4.1-milestone-1
------------------------------------------------------------

Build time:   2017-06-20 17:05:46 UTC
Revision:     3ad5af92d482b963cc6b00fb5ad53b608606d657

Groovy:       2.4.11
Ant:          Apache Ant(TM) version 1.9.6 compiled on June 29 2015
JVM:          1.8.0_112 (Oracle Corporation 25.112-b15)
OS:           Windows 10 10.0 amd64

PS D:\gradle-4.1-milestone-1>
```
5. 若出现了上述信息,代表配置成功.

## Hello World!
新建build.gradle文件,并定义一个task.`build.gradle`文件位置:`/d/gradle-4.1-milestone-1/gradle-demo/build.gradle`
运行`gradle tasks`即可查看该project下的所有任务:
```
y2222@DESKTOP-PQ6OAVI MINGW64 /d/gradle-4.1-milestone-1/gradle-demo
$ gradle tasks
:tasks

------------------------------------------------------------
All tasks runnable from root project
------------------------------------------------------------

Build Setup tasks
-----------------
init - Initializes a new Gradle build.
wrapper - Generates Gradle wrapper files.

Help tasks
----------
buildEnvironment - Displays all buildscript dependencies declared in root project 'gradle-demo'.
components - Displays the components produced by root project 'gradle-demo'. [incubating]
dependencies - Displays all dependencies declared in root project 'gradle-demo'.
dependencyInsight - Displays the insight into a specific dependency in root project 'gradle-demo'.
dependentComponents - Displays the dependent components of components in root project 'gradle-demo'. [incubating]
help - Displays a help message.
model - Displays the configuration model of root project 'gradle-demo'. [incubating]
projects - Displays the sub-projects of root project 'gradle-demo'.
properties - Displays the properties of root project 'gradle-demo'.
tasks - Displays the tasks runnable from root project 'gradle-demo'.

To see all tasks and more detail, run gradle tasks --all

To see more detail about a task, run gradle help --task <task>

BUILD SUCCESSFUL in 0s
1 actionable task: 1 executed
```
在build.gradle文件中,新建一个sayHello任务:
```
task sayHello {
	doLast {
		println 'Hello World!'
	}
}
```
运行sayHello任务,即可打印出Hello World!
```
$ gradle sayHello
Starting a Gradle Daemon (subsequent builds will be faster)
:sayHello
Hello World!

BUILD SUCCESSFUL in 3s
1 actionable task: 1 executed

```
`:syaHello`为正在执行的任务名称.
`Hello World!`为我们输出的字符串.

## Gradle Wapper
`Gradle Wapper`可以使我们在没有安装gralde的环境下运行gradle,而且可以很方便的更换gradle的版本.
```
$ gradle wrapper
:wrapper

BUILD SUCCESSFUL in 0s
1 actionable task: 1 executed

y2222@DESKTOP-PQ6OAVI MINGW64 /d/gradle-4.1-milestone-1/gradle-demo
$ ls
build.gradle  gradle/  gradlew*  gradlew.bat
```
`gradle/`文件夹下为当前项目的配置文件,可以很方便的修改gradle的版本
`gradlew`,`gradlew.bat`为unix,windows系统下的脚本文件,可以直接运行gradle脚本.比如运行上述sayHello:
```
$ ./gradlew sayHello
Downloading https://services.gradle.org/distributions/gradle-4.1-milestone-1-bin.zip
```
> 注:第一次运行,将会下载`PROJECT_ROOT/gradle/wrapper/gradle-wrapper.properties`文件中配置的gradle版本,可能时间会比较长.

以后执行gradle指令就可以使用`gradlew task_name`,记得提前进入项目根目录哦.

## 运行一个核心任务(core task)
Gradle 内置了很多type,供创建task时使用,比如`Copy`,他的作用是拷贝文件.
新建文件夹src,并在src下建立一个文件hello.txt,运行下述任务:
```
task copy(type: Copy) {
    from 'src'
    into 'dest'
}
```
将会生成dest文件夹,并拷贝hello.txt文件至dest文件夹下.

## 使用gradle插件
目前我们项目只有最基础的gradle构建能力,并不能构建一个java项目,或者android项目.
```
$ gradle tasks --all
:tasks

------------------------------------------------------------
All tasks runnable from root project
------------------------------------------------------------
...

Other tasks
-----------
copy
sayHello

BUILD SUCCESSFUL in 0s
1 actionable task: 1 executed
```
只有我们定义的2个基础方法,并且这两个方法对一个具体的项目没有什么作用.
那么如何构建一个java项目的gradle呢,这就使用的gradle plugin的概念.gradle plugin中定义了众多任务,使得我们构建某个具体的项目变得简单.
比如`java`插件可以构建java项目
```
plugins {
    id 'java'
}
```
我们再来看现在gradle有哪些任务:
```
$ gradle tasks --all
:tasks

------------------------------------------------------------
All tasks runnable from root project
------------------------------------------------------------

Build tasks
-----------
assemble - Assembles the outputs of this project.
build - Assembles and tests this project.
buildDependents - Assembles and tests this project and all projects that depend on it.
buildNeeded - Assembles and tests this project and all projects it depends on.
classes - Assembles main classes.
clean - Deletes the build directory.
jar - Assembles a jar archive containing the main classes.
testClasses - Assembles test classes.

Build Setup tasks
-----------------
init - Initializes a new Gradle build.
wrapper - Generates Gradle wrapper files.

Documentation tasks
-------------------
javadoc - Generates Javadoc API documentation for the main source code.

Help tasks
----------
...

Verification tasks
------------------
check - Runs all checks.
test - Runs the unit tests.

Other tasks
-----------
compileJava - Compiles main Java source.
compileTestJava - Compiles test Java source.
copy
processResources - Processes main resources.
processTestResources - Processes test resources.
sayHello

BUILD SUCCESSFUL in 2s
1 actionable task: 1 executed
```
`java`插件内增加了`Build Tasks`任务集,里面的`jar`任务用来打包jar包,`assemble`任务用来输出所有文件.

## 小结
- gradle环境搭建
- gradle -v # 查看gradle版本号
- gradle tasks [--all] # 查看当前所有tasks
- 创建一个task任务 `task task_name {}`
- 执行一个gradle任务 `gradle task_name`
- 使用Gradle Wapper
- 创建一个核心任务
- 使用gradle插件

----
参考
https://guides.gradle.org/creating-new-gradle-builds/