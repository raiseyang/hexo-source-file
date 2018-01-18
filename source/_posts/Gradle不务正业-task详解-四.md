title: Gradle不务正业-task详解(四)
author: raiseyang
tags:
  - gradle
categories:
  - gradle
date: 2017-10-21 15:27:00
---
# Gradle不务正业-task详解(四)


---
### Project&Task&Action
- 一个Gradle项目由一个或多个Project组成.
- 一个Project由一个或多个Task组成.
- 一个Task由一个或者多个Action组成.
- 在一个新建的默认的安卓项目中,默认存在2个Project:`project_name(root)`,`:app`
- setting.gradle文件仅做配置project使用
<!-- more -->

### Gradle执行阶段
gradle运行将会分别执行3个阶段:`initialization`,`configuration`,`execution`.

- `initialization`阶段将会识别所有project.
在`configuration`阶段,会将所有的依赖,配置都同步完成,在`execution`阶段将会使用这些依赖,配置来构建具体任务;
在`configuration`阶段执行完成之后,`execution`阶段才会执行;
比如:
```
task testPhase {
	println 'configuration phase exec 1.'
	doFirst {
		println 'execution phase exec a.'
	}
	doLast {
		println 'execution phase exec b.'
	}
	println 'configuration phase exec 2.'
}

```
`doFirst`和`doLast` action块都是在`execution`阶段执行,其余代码将会在`configuration`执行,所有应该结果应该是1,2,a,b;
实际运行结果如下:
```
$ gradle testPhase -q
configuration phase exec 1.
configuration phase exec 2.
execution phase exec a.
execution phase exec b.
```
所以在自定义Task时,编写的代码需要区分到底是在哪个阶段运行.
### Task
一个task是一个单一的构建任务,一次build任务执行时,通常会执行多个task,比如:编译生成classes,生成javaDoc文档等等;
通常我们这样创建task:
```
task myTask
task myTask { configure closure }
task myTask(type: SomeType)
task myTask(type: SomeType) { configure closure }
```
每个task都有一个name,上述task的name为myTask;因为task和project关联,所以每个具体的task都有一个全路径,根据所属project来指定,使用`:`号分隔;
下例代码,表示一个clean task的的执行过程:
```
Executing tasks: [clean]
:clean
:app:clean
:http_libs:clean
:iot_download_libs:clean
:iot_libs:clean
:mqtt_libs:clean
:trace:clean

BUILD SUCCESSFUL in 1s
7 actionable tasks: 7 executed
```
可以发现命名规则为:`:project_name:task_name`
一个task属于某个具体的project中,不同的project通常会包含同一个task,比如build;

### Task Actions
一个task由众多`Action`组成,当一个任务执行时,每个`Action`将依次执行;我们也可以自己添加`Action`,最常见的就是`doFirst`,`doLast`;
```
task clean {
    doFirst {
        println 'custom task:1'
    }
}
```


### 自定义task
> 自定义task可以在构建项目过程中，做一些自动化的事情：比如属性配置，拷贝文件，执行某段脚本等；

Gradle DSL支持task代码块，API也包含一些常用的任务：Copy,Wrapper,exec...，你也可以使用这些任务的一些简单属性来做事情；
比如：`Copy`任务包含两个属性：`from`,`into`；from属性可以配置源文件(夹),into配置目标文件(夹)。

    task copyApks(type: Copy) {
        from("$buildDir/outputs/apk") {
            exclude '**/*unsigned.apk', '**/*unaligned.apk'
        }
        into '../apks'
    }

`$buildDir`表示获取当前project的build目录；
`**`匹配所有层级目录
`*`匹配所有字符
注:不要急着去执行该任务,因为我们还不知道该任务何时执行;

如果不想使用API包含的任务来配置任务,也可以完全自定义任务;
```
task printVariantNames() {
    println 'configuration time will exec'
    doLast {
        android.applicationVariants.all { variant ->
            println variant.name
        }
    }
}
```
注意`doLast`代码块内的代码会在`execution`阶段执行,而该代码块之外的代码会在`configuration`阶段执行;

上述task作用是在打印出所有变体名称;
我们再来看一组示例:
```
task installDebugFlavors() {
    android.applicationVariants.all { v ->
        if (v.name.endsWith('Debug')) {
            String name = v.name.capitalize()
            dependsOn "install$name"
        }
    }
}
```
该task作用是安装所有Debug任务到设备中(请提前确保应用包名不一致);
`dependsOn`方法在`configuration`阶段执行,所有当我们执行该task时,先是将所有依赖的任务配置好之后,再分别执行他们;
```
./gradlew instDebFl
:app:preBuild UP-TO-DATE
:app:preArrogantDebugBuild UP-TO-DATE
:app:checkArrogantDebugManifest
// ... lots of tasks ...
:app:assembleArrogantDebug UP-TO-DATE
:app:installArrogantDebug
Installing APK 'app-arrogant-debug.apk' on 'Nexus_5_API_23(AVD) - 6.0'
Installed on 1 device.
:app:checkFriendlyDebugManifest
// ... lots of tasks ...
:app:assembleFriendlyDebug UP-TO-DATE
:app:installFriendlyDebug
Installing APK 'app-friendly-debug.apk' on 'Nexus_5_API_23(AVD) - 6.0'
Installed on 1 device.
:app:checkObsequiousDebugManifest
// ... lots of tasks ...
:app:assembleObsequiousDebug UP-TO-DATE
:app:installObsequiousDebug
Installing APK 'app-obsequious-debug.apk' on 'Nexus_5_API_23(AVD) - 6.0'
Installed on 1 device.
:app:installDebugFlavors
BUILD SUCCESSFUL
```

### 在build阶段运行自定义task
> 使用dependOn属性插入您的任务

上节拷贝apk的task是没法运行的,因为在build时,apk可能还没有生成,所有跟别谈copy了;`assemble`task是生成apk的任务,所有我们依赖在这个任务上,就可以完成copy apk的任务;
```
task copyApks(type: Copy, dependsOn: assembleDebug) {
        from("$buildDir/outputs/apk") {
            exclude '**/*unsigned.apk', '**/*unaligned.apk'
    }
    into '../apks'
}
```
`dependsOn: assembleDebug`使用dependsOn可以添加一个前置任务;
如果想要在每次build时,都copy apk,可以加入下例代码:
`build.dependsOn copyApks`


### 常用内部任务类型(Task types)
#### [Exec](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.Exec.html)
```
task stopTomcat(type:Exec) {
  workingDir '../tomcat/bin'

  //on windows:
  commandLine 'cmd', '/c', 'stop.bat'

  //on linux
  commandLine './stop.sh'

  //store the output instead of printing to the console:
  standardOutput = new ByteArrayOutputStream()

  //extension method stopTomcat.output() can be used to obtain the output:
  ext.output = {
    return standardOutput.toString()
  }
}
```

#### [Copy](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.Copy.html)
```
task copyDocs(type: Copy) {
    from 'src/main/doc'
    into 'build/target/doc'
}

//for Ant filter
import org.apache.tools.ant.filters.ReplaceTokens

//for including in the copy task
def dataContent = copySpec {
    from 'src/data'
    include '*.data'
}

task initConfig(type: Copy) {
    from('src/main/config') {
        include '**/*.properties'
        include '**/*.xml'
        filter(ReplaceTokens, tokens: [version: '2.3.1'])
    }
    from('src/main/config') {
        exclude '**/*.properties', '**/*.xml'
    }
    from('src/main/languages') {
        rename 'EN_US_(.*)', '$1'
    }
    into 'build/target/config'
    exclude '**/*.bak'

    includeEmptyDirs = false

    with dataContent
}
```

#### [Delete]()
```
task makePretty(type: Delete) {
  delete 'uglyFolder', 'uglyFile'
  followSymlinks = true
}
```

### task简写
```
task hello << {
    println 'Hello world!'
}
```

```
task hello {
    doLast{
        println 'Hello world!'
    }
}
```

### task依赖
```
task taskX(dependsOn: 'taskY') {
    doLast {
        println 'taskX'
    }
}
task taskY {
    doLast {
        println 'taskY'
    }
}
```
`dependsOn`指定依赖的任务,上述代码`taskX`依赖`taskY`,执行`taskX`时,会限制性`taskY`任务.
`<<`操作符为`doLast`简写;以上两种写法效果一样.

----
参考:
[Gradle Task](https://docs.gradle.org/current/dsl/org.gradle.api.Task.html)
https://docs.gradle.org/current/userguide/tutorial_using_tasks.html
[more_about_tasks](https://docs.gradle.org/4.2.1/userguide/more_about_tasks.html#declareTask)