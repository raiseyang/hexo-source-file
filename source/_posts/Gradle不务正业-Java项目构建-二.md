title: Gradle不务正业-Java项目构建(二)
author: raiseyang
tags:
  - gradle
categories:
  - gradle
date: 2017-10-21 14:50:00
---
# Gradle不务正业-Java项目构建(二)


---

通过上一章的内容,我们了解了gradle的基本结构,这一章通过java项目,更深入的了解.

<!-- more -->
## 创建项目文件结构
创建一个java-demo项目
```
$ mkdir java-demo
$ cd java-demo
```
使用gradle 命令手动创建java目录结构
```
$ gradle init --type java-application
:wrapper
:init

BUILD SUCCESSFUL in 1s
2 actionable tasks: 2 executed
```
构建成功后,我们看一下目前java-demo的目录结构
```
│  build.gradle
│  gradlew
│  gradlew.bat
│  settings.gradle
│
├─.gradle
│  ...
│
├─gradle
│  └─wrapper
│          gradle-wrapper.jar
│          gradle-wrapper.properties
│
└─src
    ├─main
    │  └─java
    │          App.java
    │
    └─test
        └─java
                AppTest.java
                
```
`build.gradle`为gradle脚本文件,每一个project都对应一个.
`setting.gradle`为gradle setting文件,该文件仅作为配置project使用.目前我们就一个project,可以暂时不理会.
`src`为java源码目录,`main`为主体代码区,`test`为测试代码区.

## build.gradle
- build.gradle
```
// Apply the java plugin to add support for Java
apply plugin: 'java'

// Apply the application plugin to add support for building an application
apply plugin: 'application'

// In this section you declare where to find the dependencies of your project
repositories {
    // Use jcenter for resolving your dependencies.
    // You can declare any Maven/Ivy/file repository here.
    jcenter()
}

dependencies {
    // This dependency is found on compile classpath of this component and consumers.
    compile 'com.google.guava:guava:21.0'

    // Use JUnit test framework
    testCompile 'junit:junit:4.12'
}

// Define the main class for the application
mainClassName = 'App'
```

`repositories`配置一个仓库,所有的依赖都会去该仓库下载.
`dependencies`配置该项目需要使用到的jar包
`mainClassName`,`main`方法所在的入口类.
其他文件大家也可以看一下,不过因为增加了`java`,`application`插件,我们先一下现在有哪些任务可以执行:
```
$ gradle tasks
:tasks

------------------------------------------------------------
All tasks runnable from root project
------------------------------------------------------------

Application tasks
-----------------
run - Runs this project as a JVM application

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

Distribution tasks
------------------
assembleDist - Assembles the main distributions
distTar - Bundles the project as a distribution.
distZip - Bundles the project as a distribution.
installDist - Installs the project as a distribution as-is.

Documentation tasks
-------------------
javadoc - Generates Javadoc API documentation for the main source code.


Verification tasks
------------------
check - Runs all checks.
test - Runs the unit tests.

BUILD SUCCESSFUL in 0s
1 actionable task: 1 executed

```

## run
现有有这么多与java项目相关的任务都可以执行.先执行`run`试试:
```
$ gradle run
:compileJava
:processResources NO-SOURCE
:classes
:run
Hello world.

BUILD SUCCESSFUL in 3s
2 actionable tasks: 2 executed
```
从gradle控制台,我们可以发现gradle执行了4个任务,但我们只运行了一个`run`任务.
原来gradle中的task之间可以设置依赖关系,run依赖`classes`,`classes`依赖`processResourse`和`compileJava`任务,所以被依赖的任务必须先完成才行.这和我们编译运行java代码流程一致:先编译,再运行.
```graphLR
    A[:run] -->B(:classes)
    B -->D[:compileJava]
    B -->E[:processResources]
```
想要了解更详细的task依赖关系可以参考:[gradle-task-tree](https://github.com/dorongold/gradle-task-tree)

## build
使用`build`可以构建整个java项目,将输出很多项目构建时产生的文件.
```
$ gradle build
:compileJava
:processResources NO-SOURCE
:classes
:jar
:startScripts
:distTar
:distZip
:assemble
:compileTestJava
:processTestResources NO-SOURCE
:testClasses
:test
:check
:build

BUILD SUCCESSFUL in 2s
7 actionable tasks: 7 executed

```
运行完成之后,将会在项目根目录下生成`build`目录.
该项目`.jar`文件存放在`build/libs/`目录下.
可以打开`build\reports\tests\test\index.html`文件查看测试报告.

## 打包.jar
当前若只想打包`.jar`文件,只需要执行`gradle jar`即可.但是这里需要注意打包出来的jar包是不能直接运行的,想要打包可运行的jar需要在build.gradle中加入:
```
jar {
    manifest {
        attributes 'Main-Class': 'App'
    }
    archiveName "java-demo.jar"
}
```
增加清单文件属性'Main-class',属性值为类的全包名`com.raise.xxx.Main`.
`archiveName`指定jar包的名称.
若注释有中文,还需要指定编码:
```
tasks.withType(JavaCompile) {
    options.encoding = "UTF-8"
}
```

build目录不需要我们手动删除,下次构建时会自动删除旧文件.若想清除build,可以使用clean命令.

## clean
```
$ gradle clean
:clean

BUILD SUCCESSFUL in 0s
1 actionable task: 1 executed

```
该命令将会删除当前project的build目录,若执行该任务失败,也可手动在磁盘上删除build目录,不会影响项目构建.

---
参考:
https://guides.gradle.org/building-java-applications/