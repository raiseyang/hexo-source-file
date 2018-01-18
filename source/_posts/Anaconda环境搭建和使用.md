title: Anaconda环境搭建和使用
author: raiseyang
tags: []
categories:
  - python
date: 2017-10-26 08:30:00
---
# Anaconda环境搭建和使用


---

Anaconda是可以理解为另一个python发行版本,可以使用它来编写python.它还是一个包管理工具,默认的python包有几百个.

可以将Anaconda理解为一个包管理平台,默认有它自己`anaconda`,命令行支持包`conda`,python环境`python`等.

1. 访问[Anaconda官网](https://www.anaconda.com/download/#windows),下载对应版本.
2. 打开安装包,按照指南安装即可,和普通软件没啥区别.安装过程中的注意事项在这里:[windows版本安装指南](https://docs.anaconda.com/anaconda/install/windows)
3. 安装成功之后,点开windows开始菜单,能打开`Anaconda Prompt`,则说明安装成功.
<!-- more -->

## 可视化的包管理界面
在`开始菜单`处打开`Anaconda Navigator`,可以看到一个很漂亮的导航界面.

## 命令行操作Anaconda
[命令行文档](https://conda.io/docs/user-guide/cheatsheet.html)

`conda info`查看当前Anaconda相关信息
`conda install PACKAGE_NAME`安装包
`conda -h`查看命令帮助
`conda install -h`查看`install`命令帮助,也可查看其他的命令帮助

> 如果使用conda info命令查看版本的话,会看到当前conda版本和我们下载的Anaconda版本不一致.原因就在这里,conda和anaconda是两个不同的包.查看这两个的版本可在`anaconda Navigator`的`Environments`栏查看.

## anaconda环境管理
1. 查看当前运行环境
```
(D:\Anaconda3) C:\Users\y2222\Documents>conda info --envs
# conda environments:
#
root                  *  D:\Anaconda3

```
当前的运行环境也是默认的运行环境,名称为root,路径是anaconda的路径.

2. 查看当前运行环境的python版本
```
(D:\Anaconda3) C:\Users\y2222\Documents>python --version
Python 3.6.2 :: Anaconda custom (64-bit)
```
我下载的是python3,所以默认的python版本为目前最新的版本.

3. 创建一个python2环境
```
(D:\Anaconda3) C:\Users\y2222\Documents>conda create --name snakes2 python=2
Fetching package metadata .............
Solving package specifications: .

Package plan for installation in environment D:\Anaconda3\envs\snakes2:

The following NEW packages will be INSTALLED:

    asn1crypto:      0.22.0-py27hc975345_1
    ca-certificates: 2017.08.26-h94faf87_0
    cachecontrol:    0.12.3-py27h15eca11_0
    certifi:         2017.7.27.1-py27hf761292_0
    cffi:            1.10.0-py27hb6f203f_1
    chardet:         3.0.4-py27h56c3b73_1
    colorama:        0.3.9-py27hdfe4ae1_0
    cryptography:    2.0.3-py27h1cc6ae9_1
    distlib:         0.2.5-py27hbc39c7c_0
    enum34:          1.1.6-py27h2aa175b_1
    html5lib:        0.999999999-py27h47766b3_0
    idna:            2.6-py27h1ea29d3_1
    ipaddress:       1.0.18-py27h08e1fa7_0
    lockfile:        0.12.2-py27hcad0c83_0
    msgpack-python:  0.4.8-py27he0366a1_0
    openssl:         1.0.2l-vc9h02d068b_2       [vc9]
    packaging:       16.8-py27hae1a450_1
    pip:             9.0.1-py27hdb66070_3
    progress:        1.3-py27hcb558ea_0
    pycparser:       2.18-py27hb43d16c_1
    pyopenssl:       17.2.0-py27h58ddd18_0
    pyparsing:       2.2.0-py27hc7d9fa6_1
    pysocks:         1.6.7-py27h59bdd1e_1
    python:          2.7.14-h59f5a59_20
    requests:        2.18.4-py27h3159eba_1
    setuptools:      36.5.0-py27ha483b79_0
    six:             1.11.0-py27ha5e1701_1
    urllib3:         1.22-py27hb9f5a07_0
    vc:              9-h7299396_1
    vs2008_runtime:  9.00.30729.1-hfaea7d5_1
    webencodings:    0.5.1-py27h4e224a2_1
    wheel:           0.29.0-py27hffb40b6_1
    win_inet_pton:   1.0.1-py27hf41312a_1
    wincertstore:    0.2-py27hf04cefb_0

Proceed ([y]/n)? y

#
# To activate this environment, use:
# > activate snakes2
#
# To deactivate an active environment, use:
# > deactivate
#
# * for power-users using bash, you must source
#
```
`--name`指定新环境的名称.
可以看到在创建到python2环境时,还导入了针对python2的包.
若想复制一份当前环境可使用:
```
conda create --name new_copy_env --clone snakes2
```
4. 切换到新创建的环境
```
(D:\Anaconda3) C:\Users\y2222\Documents>activate snakes2

(snakes2) C:\Users\y2222\Documents>conda info --envs
# conda environments:
#
snakes2               *  D:\Anaconda3\envs\snakes2
root                     D:\Anaconda3

(snakes2) C:\Users\y2222\Documents>python --version
Python 2.7.14 :: Anaconda, Inc.
```
使用`activate env_name`切换环境.切换成功后,可以看到命令行前显示了环境名称.
使用`deactivate`会到root环境.
5. 删除环境
```
conda remove --name snakes2 --all
```

## python包管理
pip可以安装python包,conda也可以.
1. 查看当前安装的包
```
(snakes2) C:\Users\y2222\Documents>conda list
# packages in environment at D:\Anaconda3\envs\snakes2:
#
asn1crypto                0.22.0           py27hc975345_1
ca-certificates           2017.08.26           h94faf87_0
cachecontrol              0.12.3           py27h15eca11_0
certifi                   2017.7.27.1      py27hf761292_0
cffi                      1.10.0           py27hb6f203f_1
chardet                   3.0.4            py27h56c3b73_1
colorama                  0.3.9            py27hdfe4ae1_0
cryptography              2.0.3            py27h1cc6ae9_1
distlib                   0.2.5            py27hbc39c7c_0
enum34                    1.1.6            py27h2aa175b_1
html5lib                  0.999999999      py27h47766b3_0
idna                      2.6              py27h1ea29d3_1
ipaddress                 1.0.18           py27h08e1fa7_0
lockfile                  0.12.2           py27hcad0c83_0
msgpack-python            0.4.8            py27he0366a1_0
openssl                   1.0.2l            vc9h02d068b_2  [vc9]
packaging                 16.8             py27hae1a450_1
pip                       9.0.1            py27hdb66070_3
progress                  1.3              py27hcb558ea_0
pycparser                 2.18             py27hb43d16c_1
pyopenssl                 17.2.0           py27h58ddd18_0
pyparsing                 2.2.0            py27hc7d9fa6_1
pysocks                   1.6.7            py27h59bdd1e_1
python                    2.7.14              h59f5a59_20
requests                  2.18.4           py27h3159eba_1
setuptools                36.5.0           py27ha483b79_0
six                       1.11.0           py27ha5e1701_1
urllib3                   1.22             py27hb9f5a07_0
vc                        9                    h7299396_1
vs2008_runtime            9.00.30729.1         hfaea7d5_1
webencodings              0.5.1            py27h4e224a2_1
wheel                     0.29.0           py27hffb40b6_1
win_inet_pton             1.0.1            py27hf41312a_1
wincertstore              0.2              py27hf04cefb_0
```
2. 查看python包[BeautifulSoup4]信息
```
(snakes2) C:\Users\y2222\Documents>conda search BeautifulSoup4
Fetching package metadata .............
beautifulsoup4               4.4.0                    py27_0  defaults
                             4.4.0                    py34_0  defaults
                             4.4.1                    py27_0  defaults
                             4.4.1                    py34_0  defaults
                             4.4.1                    py35_0  defaults
                             4.5.1                    py27_0  defaults
                             4.5.1                    py34_0  defaults
                             4.5.1                    py35_0  defaults
                             4.5.1                    py36_0  defaults
                             4.5.3                    py27_0  defaults
                             4.5.3                    py34_0  defaults
                             4.5.3                    py35_0  defaults
                             4.5.3                    py36_0  defaults
                             4.6.0                    py27_0  defaults
                             4.6.0                    py34_0  defaults
                             4.6.0                    py35_0  defaults
                             4.6.0                    py36_0  defaults
                             4.6.0            py27hc287451_1  defaults
                             4.6.0            py35h61fcdcc_1  defaults
                             4.6.0            py36hd4cc5e8_1  defaults
```
3. 安装一个python包
```
(snakes2) C:\Users\y2222\Documents>conda install BeautifulSoup4
Fetching package metadata .............
Solving package specifications: .

Package plan for installation in environment D:\Anaconda3\envs\snakes2:

The following NEW packages will be INSTALLED:

    beautifulsoup4: 4.6.0-py27hc287451_1

Proceed ([y]/n)? y

beautifulsoup4 100% |###############################| Time: 0:00:00 236.82 kB/s

(snakes2) C:\Users\y2222\Documents>conda search BeautifulSoup4
Fetching package metadata .............
beautifulsoup4               4.4.0                    py27_0  defaults
                             4.4.0                    py34_0  defaults
                             4.4.1                    py27_0  defaults
                             4.4.1                    py34_0  defaults
                             4.4.1                    py35_0  defaults
                             4.5.1                    py27_0  defaults
                             4.5.1                    py34_0  defaults
                             4.5.1                    py35_0  defaults
                             4.5.1                    py36_0  defaults
                             4.5.3                    py27_0  defaults
                             4.5.3                    py34_0  defaults
                             4.5.3                    py35_0  defaults
                             4.5.3                    py36_0  defaults
                             4.6.0                    py27_0  defaults
                             4.6.0                    py34_0  defaults
                             4.6.0                    py35_0  defaults
                             4.6.0                    py36_0  defaults
                          *  4.6.0            py27hc287451_1  defaults
                             4.6.0            py35h61fcdcc_1  defaults
                             4.6.0            py36hd4cc5e8_1  defaults
```
安装完成之后,再次`conda search BeautifulSoup4`查看,将会看到对应版本上有一个`*`,表示当前安装的版本.
同样的anaconda也支持pip安装python包.
> pip和conda可以作为包管理工具来安装,只是有些包pip可以安装,有些包conda可以安装.毕竟仓库不同.
## 镜像服务
```
conda config --add channels 'https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/'
conda config --set show_channel_urls yes
```
网络上流行的配置国内镜像,不过现在国内访问anaconda已经很快了.就可以不配置了.
## PyCharm使用anaconda环境
在配置路径`File`->`Setting...`->`Project:xx`->`ProjectInterpreter`处选择`D:\Anaconda3\python.exe`即可切换成anaconda环境.
同理若有其他anaconda环境,则选择对应的路径下的`python.exe`即可.比如:`D:\Anaconda3\envs\snakes2`.该路径可能需要自己添加.
## 更多资料
[github书籍](https://conda.io/docs/user-guide/getting-started.html)
[conda指令大全](https://conda.io/docs/user-guide/cheatsheet.html)
