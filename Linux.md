# Javaweb第二十九天笔记

## 内容回顾

## 今日重点


Linux  系统
MacOS
主机 + 系统
硬件 +  软件

需求分析  开发  测试   上线(把代码打包发布到服务器(Linux系统)) 
路由器
手机系统 Android
linux git
版本控制工具



1. Linux常用命令

### 01-Linux的简介.avi

什么是Linux
Linux是基于Unix的,是一种自由和开放源码的操作系统，存在着许多不同的Linux版本，但它们都使用了Linux内核。
Linux的应用
服务器系统: Web应用服务器、数据库服务器、接口服务器、DNS、FTP等等
嵌入式系统: 路由器、防火墙、手机、PDA、IP 分享器、交换器、家电用品的微电脑控制器等等
高性能运算、计算密集型应用 Linux有强大的运算能力
桌面应用系统
移动手持系统
Linux的主流版本
Ubuntu, Centos, redhat, fedora, freebsd...

### 02-虚拟软件的安装.avi

安装参考《虚拟软件vmware安装.doc》

### 03-CentOS的安装.avi

参考《CentOS6详细安装文档.doc》

### 04-Linux的目录结构.avi

![](http://os4z8t7lb.bkt.clouddn.com/201801222253_456.png)

### 05-Linux的常用命令：cd命令.avi

==切换目录==
`cd app` 切换到app目录
`cd ..` 切换到上一层目录
`cd /` 切换到系统根目录
`cd ~` 切换到用户主目录
`cd -` 切换到上一个所在目录

### 06-Linux的常用命令：ls、ll、dir命令.avi

==列出文件列表==
ls(list)是一个非常有用的命令，用来显示当前目录下的内容。配合参数的使用，能以不同的方式显示目录内容
格式：ls[参数][路径或文件名]

在linux中以 `.` 开头的文件都是隐藏的文件
`ls`
`ls -a`  显示所有文件或目录（包含隐藏的文件）
`ls -l`  缩写成ll

### 07-Linux的常用命令：mkdir、rmdir命令.avi

==创建目录==
`mkdir app` 在当前目录下创建app目录
`mkdir –p app2/test` 级联创建aap2以及test目
==移除目录==
`rmdir(remove directory)`命令可用来删除“空”的子目录
`rmdir app`  删除app目录

### 08-Linux的常用命令：cat、more、less、tail命令.avi

浏览文件

`cat` 用于显示文件的内容
格式：cat[参数]<文件名>
`cat yum.conf`

`more` 一般用于要显示的内容会超过一个画面长度的情况。按空格键显示下一个画面。回车显示下一行内容。按 q 键退出查看。
`more yum.conf` 空格显示下一页数据  回车显示下一行的数据

`less` 用法和`more`类似，不同的是less可以通过PgUp、PgDn键来控制
`less yum.conf`  PgUp 和 PgDn 进行上下翻页.

`tail`命令是在实际使用过程中使用非常多的一个命令，它的功能是：用于显示文件后几行的内容。
==`tail -10 /etc/passwd`  查看后10行数据==
`tail -f catalina.log`  ==动态查看日志==
`ctrl+c` 结束查看

### 09-Linux的常用命令：rm命令.avi

==文件删除操作==
`rm`  删除文件
`rm a.txt`  删除a.txt文件
删除需要用户确认，y/n rm 删除不询问
`rm -f a.txt`  不询问，直接删除 rm 删除目录
`rm -r a`  递归删除
不询问递归删除（慎用）
`rm -rf  a`  不询问递归删除
`rm -rf *`    删除所有文件
`rm -rf /*`   自杀

rm 文件名
rm -r 文件目录名称
rm -rf 删除不询问
rm -rf /* #超级管理员 $
rmdir  .....

### 10-Linux的远程连接：CRT的软件的安装和使用.avi

### 11-Linux的常用命令：cp、mv命令.avi

文件的拷贝和剪切
cp(copy)命令可以将文件从一处复制到另一处。一般在使用cp命令时将一个文件复制成另一个文件或复制到某目录时，需要指定源文件名与目标文件名或目录。
`cp a.txt b.txt`   将a.txt复制为b.txt文件
`cp a.txt ../`     将a.txt文件复制到上一层目录中 

mv 移动或者重命名
`mv a.txt ../`   将a.txt文件移动到上一层目录中
`mv a.txt b.txt` 将a.txt文件重命名为b.txt

### 12-Linux的常用命令：tar命令.avi

==打包或者解压==
tar命令位于/bin目录下，它能够将用户所指定的文件或目录打包成一个文件，但不做压缩。
一般Linux上常用的压缩方式是选用tar将许多文件打包成一个文件，再以gzip压缩命令压缩成xxx.tar.gz(或称为xxx.tgz)的文件。
常用参数：
`-c:`创建一个新tar文件 
`-v:`显示运行过程的信息 
`-f:`指定文件名 
`-z:`调用gzip压缩命令进行压缩 
`-t:`查看压缩文件的内容 
`-x:`解开tar文件

==打包==
`tar –cvf xxx.tar ./*`
打包并且压缩：
`tar –zcvf xxx.tar.gz ./*`

==解压==
`tar –xvf xxx.tar`
`tar -xvf xxx.tar.gz -C /usr/aaa`

### 13-Linux的常用命令：grep命令.avi

grep命令
查找符合条件的字符串
用法: grep [选项]... PATTERN [FILE]...
示例：
`grep lang anaconda-ks.cfg`  在文件中查找lang
`grep lang anaconda-ks.cfg –color` 高亮显示

### 14-Linux的常用命令：其他常用命令.avi

其他常用命令
`pwd` ==显示当前所在目录==
`touch` ==创建一个空文件==
`touch a.txt`
`ll -h` 友好显示文件大小
`wget` 下载资料 `wget http://nginx.org/download/nginx-1.9.12.tar.gz`

### 15-Linux的常用命令：VI和VIM编辑器.avi

==Vi和Vim编辑器(传奇-神器 编辑器之神)==
在Linux下一般使用vi编辑器来编辑文件
vi既可以查看文件也可以编辑文件
三种模式：命令行、插入、底行模式
切换到命令行模式：按Esc键
切换到插入模式：按 i 、o、a键
`i` 在当前位置生前插入
`I` 在当前行首插入
`a` 在当前位置后插入
`A` 在当前行尾插入
`o` 在当前行之后插入一行
`O` 在当前行之前插入一行

切换到底行模式：按 `:`（冒号）

打开文件：`vim file`
退出：`esc :q`
修改文件：输入`i`进入插入模式
保存并退出：`esc :wq`
不保存退出：`esc :q!`

### 16-Linux的常用命令：重定向输出.avi

`>`  重定向输出，覆盖原有内容
`>>` 重定向输出，又追加功能

`cat /etc/passwd > a.txt`  将输出定向到a.txt中
`cat /etc/passwd >> a.txt`  输出并且追加

`ifconfig > ifconfig.txt`

### 17-Linux的常用命令：管道和命令执行控制.avi

管道是Linux命令中重要的一个概念，其作用是将一个命令的输出用作另一个命令的输入

`ls --help | more`  分页查询帮助信息
`ps –ef | grep java`  查询名称中包含java的进程
`ifconfig | more`
`cat index.html | more`
`ps –ef | grep aio`

命令控制
命令之间使用 `&&` 连接，实现逻辑与的功能
只有在 `&&` 左边的命令返回真,`&&` 右边的命令才会被执行
只要有一个命令返回假,后面的命令就不会被执行。
`mkdir test && cd test`

### 18-Linux的常用命令：网络通讯的命令.avi

网络通讯命令
`ifconfig`  显示或设置网络设备
`ifconfig eth0 up` 启用eth0网卡
`ifconfig eth0 down`  停用eth0网卡 ping   探测网络是否通畅。
`ping 192.168.0.1 netstat` 查看网络端口。
`netstat -an | grep 3306` 查询3306端口占用情况

### 19-Linux的常用命令：系统管理的命令.avi

系统管理命令
`date` 显示或设置系统时间
`date`  显示当前系统时间
`date -s “2014-01-01 10:10:10“`  设置系统时间 df 显示磁盘信息
`df –h`  友好显示大小 free 显示内存状态
`free –m` 以mb单位显示内存组昂头 top 显示，管理执行中的程序

`clear` 清屏幕
`ps` 正在运行的某个进程的状态
`ps –ef`  查看所有进程
`ps –ef | grep ssh` 查找某一进程 kill 杀掉某一进程
`kill 2868`  杀掉2868编号的进程
`kill -9 2868`  强制杀死进程

`du` 显示目录或文件的大小
`du –h` 显示当前目录的大小
`who` 显示目前登入系统的用户信息
`hostname` 查看当前主机名
修改：`vi /etc/sysconfig/network`
`uname` 显示系统信息。
`uname -a` 显示本机详细信息
依次为：内核名称(类别)，主机名，内核版本号，内核版本，内核编译日期，硬件名，处理器类型，硬件平台类型，操作系统名称

### 20-Linux的常用命令：用户管理的命令.avi

用户管理命令
`useradd` 添加一个用户
`useradd test` 添加test用户
`useradd test -d /home/t1`  指定用户home目录
`passwd`  设置、修改密码
`passwd test`  为test用户设置密码

切换登录：
`ssh -l test -p 22 192.168.19.128`
`su – 用户名`
`userdel` 删除一个用户
`userdel test` 删除test用户(不会删除home目录)
`userdel –r test`  删除用户以及home目录

### 21-Linux的常用命令：组管理的命令.avi

组管理命令
当在创建一个新用户user时，若没有指定他所属于的组，就建立一个和该用户同名的私有组
创建用户时也可以指定所在组
`groupadd`  创建组
`groupadd public`  创建一个名为public的组
`useradd u1 –g public`  创建用户指定组 groupdel 删除组，如果该组有用户成员，必须先删除用户才能删除组。
`groupdel public`


### 22-Linux的常用命令：权限管理.avi

文件权限
![](http://os4z8t7lb.bkt.clouddn.com/201801231353_92.png)
变更文件权限
`chmod` 变更文件或目录的权限。
`chmod 755 a.txt`
`chmod u=rwx,g=rx,o=rx a.txt`
`chmod 000 a.txt  / chmod 777 a.txt chown` ==变更文件或目录改文件所属用户和组==
`chown u1:public a.txt` ：变更当前的目录或文件的所属用户和组
`chown -R u1:public dir` ：变更目录中的所有的子目录及文件的所属用户和组

管理员模式运行CMD:
netsh winsock reset
重启电脑
解决开机黑屏

作业
进入/home目录
创建一个你名字的文件夹
在文件夹下面创建几个文件
使用vim写内容
然后把这几个文件进行压缩
然后在创建一个文件夹
把刚才的压缩文件解压到新创建的文件夹下