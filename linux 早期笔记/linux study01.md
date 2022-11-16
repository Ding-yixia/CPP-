# 各种目录

[TOC]

根目录

```
cd    /
```

家目录   (home) 是所有用户的目录，存放所有用户

```
cd  ~
```

 /bin 

二进制目录 存放使用的命令

/boot 

开机启动的文件，镜像 连接文件

/dev 

device ,存放外部设备

/etc 

配置文件

/lib

动态连接库文件（共享库）

/media

外设   系统自动识别的设备 U盘  自动挂载

/mnt  

手动挂载的外设备

/opt

软件安装目录

/proc

虚拟目录，系统内存的映射

/root 

系统管理员目录

/usr

应用程序和文件存放的目录 （windows 下的 program files）

/var 

存放经常修改的文件  ，日志 

##  用户目录

cd .

点 代表当前目录 

cd .. 上级目录

#### 回到家目录的方式

cd ~   回到 家目录   （普通用户家目录是 /home/用户名,  root 用户的家目录是  snap）

```
xxx@xxx-virtual-machine:~$ cd 下载
xxx@xxx-virtual-machine:~/下载$ pwd
/home/xxx/下载

```

/home/xxx/  就是      ~

直接 cd  也可以切换到家目录

```
root@xxx-virtual-machine:/home/xxx/下载# cd 
root@xxx-virtual-machine:~# ls
snap

```

也可以使用相对路径

```
cd /home/xxx(用户)
```

#### **root用户和普通用户的辨别**

```
root@xxx-virtual-machine:~# 
```

后面为# 为root 用户

```
xxx@xxx-virtual-machine:~$ 
```

后面为 $ 为 普通用户



cd  -  

#### **cd 短线 能在相邻的父子目录切换**

```
xxx@xxx-virtual-machine:~$ cd -
/home/xxx/下载
xxx@xxx-virtual-machine:~/下载$ cd -
/home/xxx
xxx@xxx-virtual-machine:~$ cd -
/home/xxx/下载
xxx@xxx-virtual-machine:~/下载$ cd -
/home/xxx
xxx@xxx-virtual-machine:~$ cd -
/home/xxx/下载
xxx@xxx-virtual-machine:~/下载$ cd -
/home/xxx
xxx@xxx-virtual-machine:~$ cd -
/home/xxx/下载
```

#### **当前目录的查看**

使用pwd

```
xxx@xxx-virtual-machine:~/下载$ pwd
/home/xxx/下载
```

也可以直接看 波浪线 ~ 和 $(#) 之间 的内容

```
xxx@xxx-virtual-machine:~/下载$ ls          //是在家目录的下载文件夹中
 firefox.tmp  'Steam _linux_x64_v2.7.2.deb'
xxx@xxx-virtual-machine:~/下载$ cd firefox.tmp     
xxx@xxx-virtual-machine:~/下载/firefox.tmp$ ls  	//是在家目录的下载的firefox.tmp文件夹下

```



### 文件目录颜色(大部分)

```
白色--普通文件
蓝色--目录
绿色--可执行文件
红色--压缩文件
青色-链接文件   快捷方式
黄色--设备文件
块设备 （硬盘） 字符设备（键盘） 管道 () -------  tty
灰色--其他文件
```





### linux中文件详细信息中各部分的含义

```
xxx@xxx-virtual-machine:~/下载$ ll
总用量 43180
drwxr-xr-x  3 xxx xxx     4096  6月 14 16:13  ./
drwxr-x--- 19 xxx xxx     4096  6月 18 18:57  ../
drwx------  3 xxx xxx     4096  6月  2 11:40  firefox.tmp/
-rw-rw-r--  1 xxx xxx 44201398  6月 14 16:13 'Steam _linux_x64_v2.7.2.deb'

```

 

这些字段一 一对应：**文件类型与权限  链接数  文件主  文件大小 创建或最近修改的时间  文件名**

**9个 权限 分为三组   ，依次为  文件所有者 ，文件所在组， 其他组**

①第一个字段中第一个字符表示文件类型，所用字符含义如下：

    -：普通文件
    d：目录
    b：快文件
    c：字符设备文件
    l：符号链接文件
    s：套接字文件（socket）
    p：命名管道文件（pipe）

②第一个字段的后9个字符表示文件的存取权限，各权限字符表示如下：

    r：读
    w：写
    x：执行。对于目录，表示可以访问该目录。
    s：当文件被执行时，把该文件的UID或GID赋予执行进程UID（用户ID）或GID（组ID）。
    t：设置粘着标志位（留在内存，不被换出），若是目录，则在该目录中的文件只能被超级用户（root）、文件主删除；若是可执行文件，则在该文件执行后，指向其正文段的指针仍留在内存。这样再次执行它时，系统能更快地装入该文件。
    -：表示没有设置权限。

 ③对于符号链接文件，在最后“文件名”字段显示的形式是：

符号链接文件名->目标文件的路径名

④对于设备文件，其“文件大小”字段显示的信息是设备的主、次设备号。

列表的第一行给出该目录的总块数，其中包含了间接块。

五    3，19，3  表示硬链接数量，就是文件别名的数量。

  前一个 xxx 是该文件所有者的名称

后一个xxx 是文件所属组的名称

对目录而言  大小 均为 4096

对某个文件 而已 大小就是 文件的大小



## 一些命令

##### tree

```
xxx@xxx-virtual-machine:~$ tree 下载
下载
├── firefox.tmp
│   ├── Temp-219640a1-9a33-43b3-b75a-5b296421280e
│   └── tmpaddon
└── Steam _linux_x64_v2.7.2.deb

```

这里   tree 是 下载 相对于 家目录 ~ 的相对路径

##### mkdir 

创建多级目录需要加  -p 

```
xxx@xxx-virtual-machine:~/下载/firefox.tmp$ mkdir test			 //创建单级目录

xxx@xxx-virtual-machine:~/下载/firefox.tmp$ mkdir test/01/02/03 -p      //创建多级目录 ， -p 参数可以放 mkdir后面

xxx@xxx-virtual-machine:~/下载/firefox.tmp$ tree test
test
└── 01
    └── 02
        └── 03

```

##### rmdir  

##### 只能 删除空目录

##### rm  -rf  

递归强制删除

##### touch 

创建文件（普通文件  -）

```
touch linux
-rw-rw-r-- 1 xxx xxx      0  6月 18 19:45 linux
```

也可以更新文件创建的时间 （当文件之前就存在的时候）

```
touch linux
-rw-rw-r-- 1 xxx xxx      0  6月 18 19:48 linux
```

##### **cp**     

拷贝文件

```
cp linux linux02     // cp 原件 副本
```

如果 右边的 linux02 不存在，就会创建 linux02

如果右边的 linux02 存在 ，就会 覆盖掉 linux02 旧有的内容

**拷贝目录 需要 递归 加 -r**

##### 文件查看命令（了解）

##### cat  （主要）

查看文件内容   

##### **more** 

查看文件部分内容（后续内容 空格 换一屏，回车 换一行显示）

##### less 

查看部分文件内容 ， 

- ctrl + F - 向前移动一屏
- ctrl + B - 向后移动一屏
- ctrl + D - 向前移动半屏
- ctrl + U - 向后移动半屏

按q   CTRL +C 退出

##### head

显示前10行

##### tail  

显示后10行

head -n 文件名

查看指定 n 行

##### mv

同目录下作用是  改名

```
mv  旧名字  新名字
```

不同目录是移动

```
mv 文件名  相对地址/绝对地址
```

##### **ln**

###### 创建软链接 （快捷方式）

ln -s   文件（目录） （软链接名）快捷方式名

文件（目录）使用绝对路径 ，可以防止当软链接名 被 移动时，出现 链接失效的情况 

```
root@xxx-virtual-machine:/home/xxx/test/test# touch a
root@xxx-virtual-machine:/home/xxx/test/test# ln a a_link -s   //绿色的
```

```
root@xxx-virtual-machine:/home/xxx/test/test# mv a_link ../       //把软链接名 移动到上级目录

root@xxx-virtual-machine:/home/xxx/test/test# cd ../
root@xxx-virtual-machine:/home/xxx/test# ll

lrwxrwxrwx  1 root root    1  6月 18 22:32 a_link -> a        // 软链接名变红色
drwxr-xr-x  2 root root 4096  6月 18 22:33 test/

```

###### **软链接的大小**

软链接的大小 取决于他 指向的这个文件的文件名字的大小

```
root@xxx-virtual-machine:/home/xxx/test/test# touch 12
root@xxx-virtual-machine:/home/xxx/test/test# touch 1234
root@xxx-virtual-machine:/home/xxx/test/test# touch 123456789
root@xxx-virtual-machine:/home/xxx/test/test# ln 12 a -s
root@xxx-virtual-machine:/home/xxx/test/test# ln 1234 b -s
root@xxx-virtual-machine:/home/xxx/test/test# ln 123456789  c -s
```

创建 3 个文件 ，文件名大小 是 2 4 3 。创建软连接 a b c

```
drwxr-xr-x 2 root root 4096  6月 18 22:45 ./
drwxr-xr-x 3 root root 4096  6月 18 22:44 ../
-rw-r--r-- 1 root root    0  6月 18 22:44 12
-rw-r--r-- 1 root root    0  6月 18 22:44 1234
-rw-r--r-- 1 root root    0  6月 18 22:45 123456789
lrwxrwxrwx 1 root root    2  6月 18 22:45 a -> 12
lrwxrwxrwx 1 root root    4  6月 18 22:45 b -> 1234
lrwxrwxrwx 1 root root    9  6月 18 22:45 c -> 123456789
```

查看 文件大小      ，三个文件没有内容 ，所以 大小 是0 。     软连接的大小是 文件名的大小

我们的**软链接会写上链接文件的文件名。一个字母一个字节，所以是9个字节，所以软链接的大小是9。仅仅是快捷方式**

**软连接也可以给目录创建快捷方式**

###### 硬链接的创建方式 

```
ln  文件名   硬链接名
```

###### **硬链接的原理**

假设数据保存在某个存储单元中， 我们对这个存储单元的标识 就是一个叫  inode 结点的东西  ， 通过这个 i   结点来 索引它  

我们可以使用 stat 文件名查看  他的 inode 是多少

```
touch a
root@xxx-virtual-machine:/home/xxx/test/test# stat a
  文件：a
  大小：0         	块：0          IO 块：4096   普通空文件
设备：803h/2051d	Inode：1707310     硬链接：1
权限：(0644/-rw-r--r--)  Uid：(    0/    root)   Gid：(    0/    root)
最近访问：2022-06-18 23:14:49.198462456 +0800
最近更改：2022-06-18 23:14:49.198462456 +0800
最近改动：2022-06-18 23:14:49.198462456 +0800
创建时间：2022-06-18 23:14:49.198462456 +0800
root@xxx-virtual-machine:/home/xxx/test/test# 
```

l 对应用于同一文件系统上的一个物理文件

l 每个目录引用相同的inode 号

l 创建时链接数递增

l 删除文件时，递减链接数，当链接数为0时，该文件已被删除（除非没有进程占用此文件）

l 硬链接的建立是不能越驱动器或分区的

事实也是如此 ，当我们创建一个文件时,其链接数便为 1

```
t@xxx-virtual-machine:/home/xxx/test/test# touch a
root@xxx-virtual-machine:/home/xxx/test/test# ll  a
-rw-r--r-- 1 root root 0  6月 18 23:14 a
```

当我们创建多个硬链接 时，就会有多个索引，映射到磁盘上的某个位置。

###### 硬链接的大小

当创建 1个空文件a ,   5个硬链接 指向 a 时 。 硬链接数为 6（创建时会有一个）

```
root@xxx-virtual-machine:/home/xxx/test/test# touch a
root@xxx-virtual-machine:/home/xxx/test/test# ln a 1
root@xxx-virtual-machine:/home/xxx/test/test# ln a 2
root@xxx-virtual-machine:/home/xxx/test/test# ln a 3
root@xxx-virtual-machine:/home/xxx/test/test# ln a 4
root@xxx-virtual-machine:/home/xxx/test/test# ln a 5
root@xxx-virtual-machine:/home/xxx/test/test# ll
总用量 8
drwxr-xr-x 2 root root 4096  6月 18 23:41 ./
drwxr-xr-x 3 root root 4096  6月 18 23:14 ../
-rw-r--r-- 6 root root    0  6月 18 23:41 1
-rw-r--r-- 6 root root    0  6月 18 23:41 2
-rw-r--r-- 6 root root    0  6月 18 23:41 3
-rw-r--r-- 6 root root    0  6月 18 23:41 4
-rw-r--r-- 6 root root    0  6月 18 23:41 5
-rw-r--r-- 6 root root    0  6月 18 23:41 a

```

当我们向 a 中写入数据的时候 （下图） ，  因为硬链接关联着我们的源文件，所以源文件的大小是多大，它们就是多大。

```
root@xxx-virtual-machine:/home/xxx/test# echo "123456789" >> a
root@xxx-virtual-machine:/home/xxx/test# cat a
123456789
root@xxx-virtual-machine:/home/xxx/test# ll
总用量 36
drwxr-xr-x  3 root root 4096  6月 18 23:47 ./
drwxr-x--- 19 xxx  xxx  4096  6月 18 21:30 ../
-rw-r--r--  6 root root   10  6月 18 23:47 1
-rw-r--r--  6 root root   10  6月 18 23:47 2
-rw-r--r--  6 root root   10  6月 18 23:47 3
-rw-r--r--  6 root root   10  6月 18 23:47 4
-rw-r--r--  6 root root   10  6月 18 23:47 5
-rw-r--r--  6 root root   10  6月 18 23:47 a
drwxr-xr-x  2 root root 4096  6月 18 23:47 test/
root@xxx-virtual-machine:/home/xxx/test# 

```

但    硬链接的文件属性中的大小就是就是inode号对应的数据块的大小，实际上 硬链接 是不占用物理空间的

  参考   https://blog.csdn.net/wangjjmiao/article/details/116210796

硬链接文件完全等同于原文件，原文件名和连接文件名都
指向相同的物理地址。不可以跨文件系统，也不可以建立目录的
硬链接。( 特点: 1.不能跨分区; 2.不能给目录做硬链接)
软链接文件相当于为原文件建立了一个快捷方式，可以跨
文件系统，也可以为目录建立。

###### 删除硬链接**

```
unlink("文件名")
```

当文件的硬链接数目为0，并不意味着文件被删除。只是说此文件具备了被删除的可能，当硬链接数目为0，若有进程占用该文件，则进程结束时，该文件将被删除

## **文件目录属性**

##### wc (了解)

获取文本文件的  行数 单词数  文字个数 文件名

```
root@xxx-virtual-machine:/home/xxx# rm a
root@xxx-virtual-machine:/home/xxx# touch a
root@xxx-virtual-machine:/home/xxx# echo 123456 >> a 
root@xxx-virtual-machine:/home/xxx# cat a
123456
root@xxx-virtual-machine:/home/xxx# wc a
1 1 7 a
root@xxx-virtual-machine:/home/xxx# echo 7890 >> a
root@xxx-virtual-machine:/home/xxx# cat a
123456
7890
root@xxx-virtual-machine:/home/xxx# wc a
 2  2 12 a
root@xxx-virtual-machine:/home/xxx# 

```

##### **od   （了解）**

查看二进制文件

```
vim test.c
gcc test.c  //产生可执行文件    
od test.o 
```

```
od test.o
```

##### du (掌握)

显示每个文件和目录的磁盘使用空间

```
xxx@xxx-virtual-machine:~/下载$ du -lh
4.0K	./test
4.0K	./firefox.tmp/test/01/02/03
8.0K	./firefox.tmp/test/01/02
12K	./firefox.tmp/test/01
16K	./firefox.tmp/test
4.0K	./firefox.tmp/Temp-219640a1-9a33-43b3-b75a-5b296421280e
524K	./firefox.tmp
50M	.
```

##### df （掌握）

```
df命令 用于显示磁盘分区上的可使用的磁盘空间。默认显示单位为KB。可以利用该命令来获取硬盘被占用了多少空间，目前还剩下多少空间等信息。
```

```
xxx@xxx-virtual-machine:~/下载$ df -lh
文件系统        容量  已用  可用 已用% 挂载点
tmpfs           188M  1.8M  187M    1% /run
/dev/sda3        59G   13G   43G   23% /
tmpfs           940M     0  940M    0% /dev/shm
tmpfs           5.0M  4.0K  5.0M    1% /run/lock
vmhgfs-fuse     358G   62G  296G   18% /mnt/hgfs
/dev/sda2       512M  5.3M  507M    2% /boot/efi
tmpfs           188M   80K  188M    1% /run/user/127
tmpfs           188M   72K  188M    1% /run/user/1000
```

##### which 

查看命令存在的目录（绝对路径）

```
xxx@xxx-virtual-machine:~/下载$ which cd
xxx@xxx-virtual-machine:~/下载$ which help
xxx@xxx-virtual-machine:~/下载$ which git
/usr/bin/git
```

但是像 cd 这样的

cd 这个常用的命令竟然找不到啊！为什么呢？这是因为 cd 是bash 内建的命令！ 但是 which 默认是找 PATH 内所规范的目录，所以当然一定找不到的！

## 文件权限用户 用户组

##### whoami

查看当前用户

```
xxx@xxx-virtual-machine:~/下载$ whoami
xxx
```

##### 文件权限设置

```
chmod [who] [+-=] [mode]  文件名
```

who :  文件所有者  u  ;  文件所属组 g ; 其它人  o ;    所有的人   a ;

```
+  增加权限
- 减少权限
= 覆盖原来的权限
```

mode :

r 读  4 ；  w 写 2 ；   x  执行 1;    - 无权限 

对单个文件 

###### 用文字描述

```
root@xxx-virtual-machine:/home/xxx/test# chmod u=- test
root@xxx-virtual-machine:/home/xxx/test# ll test
----r--r-- 1 root root 0  6月 19 14:28 test
root@xxx-virtual-machine:/home/xxx/test# 
```

```
root@xxx-virtual-machine:/home/xxx/test# chmod u=rwx test
root@xxx-virtual-machine:/home/xxx/test# ll test
-rwxr--r-- 1 root root 0  6月 19 14:28 test*
root@xxx-virtual-machine:/home/xxx/test# 
```

###### 用数字描述

```
root@xxx-virtual-machine:/home/xxx/test# chmod 777 test
root@xxx-virtual-machine:/home/xxx/test# ll test
-rwxrwxrwx 1 root root 0  6月 19 14:28 test*
root@xxx-virtual-machine:/home/xxx/test# chmod 124 test
root@xxx-virtual-machine:/home/xxx/test# ll test
---x-w-r-- 1 root root 0  6月 19 14:28 test*
```

加权限就在数字前面 加一个   +  







##### **对文件夹文件设置权限** 

和给文件夹设置权限相同



| 权限      | 对文件的影响   | 对目录的影响           |
| --------- | -------------- | ---------------------- |
| r（读取） | 可读取文件内容 | 可列出目录内容  ls     |
| w（写入） | 可修改文件内容 | 可在目录中创建删除内容 |
| x（执行） | 可作为命令执行 | 可访问目录内容  cd     |

**给文件夹里所有文件设置权限要加  -R**   



###### 修改文件所有者     

chown   用户名   文件

###### 修改文件所有组   

 chgrp    组名  文件     

###### 修改文件所有者和组  

  chown     用户名：组名   文件

注意 均需要在root用户权限下    即  sudo  chown    / chgrp

增加四个用户

```
adduser zzz  xxx  ccc vvv
```

在xxx中创建 t 文件

```
xxx@xxx-virtual-machine:~$ touch t
xxx@xxx-virtual-machine:~$ ll t

-rw-rw-r-- 1 xxx xxx 0  6月 19 17:40 t        ##文件初始状态

xxx@xxx-virtual-machine:~$ chown ccc t
chown: 正在更改't' 的所有者: 不允许的操作
xxx@xxx-virtual-machine:~$ sudo chown ccc t        ##改变所有者
xxx@xxx-virtual-machine:~$ ll t
-rw-rw-r-- 1 ccc xxx 0  6月 19 17:40 t

xxx@xxx-virtual-machine:~$ chgrp vvv t
chgrp: 正在更改't' 的所属组: 不允许的操作
xxx@xxx-virtual-machine:~$ sudo chgrp vvv t 		##改变所有组
xxx@xxx-virtual-machine:~$ ll t
-rw-rw-r-- 1 ccc vvv 0  6月 19 17:40 t


xxx@xxx-virtual-machine:~$ sudo chown xxx:zzz t    ##修改文件所有者和组
xxx@xxx-virtual-machine:~$ ll t
-rw-rw-r-- 1 xxx zzz 0  6月 19 17:40 t
```

## 文件查找

文件名

find  +查找目录 + -name  + "文件名"     引号一定要加

其他内容查看

https://blog.csdn.net/qq_55125921/article/details/123456183

##### 按内容检索

grep -r "查找内容" 查找路径   （和find命令相反）

## U盘挂载

自己挂载在   / mnt 文件夹中

电脑识别挂载在 /media中

全屏虚拟机，后 插入优盘， 因为自动会挂载。所以先卸载

查看挂载的U盘

```
xxx@xxx-virtual-machine:/$ cd media/
xxx@xxx-virtual-machine:/media$ ls
xxx
xxx@xxx-virtual-machine:/media$ ls xxx
新加卷
```

卸载挂载的U盘   sudo  umount   物理设备名

```
xxx@xxx-virtual-machine:/media/xxx$ sudo umount 新加卷
xxx@xxx-virtual-machine:/media/xxx$ ll
总用量 8
drwxr-x---+ 2 root root 4096  6月 19 21:14 ./
drwxr-xr-x  3 root root 4096  6月  2 10:19 ../

```

重新挂载到    /mnt 上

先要查看U盘的逻辑设备名

```
sudo00  fdisk -l
```

```
Disk /dev/sdb：57.62 GiB，61872793600 字节，120845300 个扇区
Disk model: DataTraveler 3.0
单元：扇区 / 1 * 512 = 512 字节
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x73736572

```

U盘的标识符是   /dev/sdb

sudo mount  标识符   挂载目录（/mnt）

```
xxx@xxx-virtual-machine:~$ sudo mount /dev/sdb  /mnt 
[sudo] xxx 的密码： 
xxx@xxx-virtual-machine:~$ cd ../../mnt/
xxx@xxx-virtual-machine:/mnt$ ls
 1                                       'System Volume Information'       VMware_V16.2.2_XiTongZhiJia.zip
 四Linux高并发服务器开发（C_C++阶段四）   ubuntu-22.04-desktop-amd64.iso
xxx@xxx-virtual-machine:/mnt$ 

```

### 磁盘设备种类

sd --> SCSI Device
hd --> Hard Disk硬盘
fd --> F1oppy Disk软盘

![image-20220619221001801](image-20220619221001801.png)



# 压缩管理

##### gzip 和  bzip       

对应的 解压命令是 gunzip   bunzip

不会保留 原文件  ，不会打包  (bzip -k 可以保留原文件，而且能 压缩目录)

```
xxx@xxx-virtual-machine:~/下载/test01$ touch {1..6}.txt
xxx@xxx-virtual-machine:~/下载/test01$ ls
1.txt  2.txt  3.txt  4.txt  5.txt  6.txt
```

```
xxx@xxx-virtual-machine:~/下载/test01$ ls
1.txt.gz  2.txt.gz  3.txt.gz  4.txt.gz  5.txt.gz  6.txt.gz

```

##### tar    

参数

c  --压缩 ；  x  --解压缩   v --显示提示信息    f  指定文件名字

**如果不指定   z  或者是   j    该命令（tar） 只能 打包文件**

z 是采用gzip 方式压缩  （能压缩目录）     j 是采用 bzip 方式压缩(不能压缩目录)

**压缩命令 ：**

``` 
tar  -zvcf  压缩后的文件名   被压缩的文件名  -C  压缩到的目录
tar  -jvcf  压缩后的文件名字   被压缩的文件或者目录 -C  压缩到的目录
```

```
xxx@xxx-virtual-machine:~/下载/t$ touch {1..6}.txt
xxx@xxx-virtual-machine:~/下载/t$ ls
1.txt  2.txt  3.txt  4.txt  5.txt  6.txt
xxx@xxx-virtual-machine:~/下载/t$ tar zvcf alltxt *.txt
1.txt
2.txt
3.txt
4.txt
5.txt
6.txt
xxx@xxx-virtual-machine:~/下载/t$ 
```

**解压缩**

tar   zvxf（jvxf）压缩包名  -C 解压路径

```
xxx@xxx-virtual-machine:~/下载/t$ rm *.txt
xxx@xxx-virtual-machine:~/下载/t$ ls
alltxt

```



```
xxx@xxx-virtual-machine:~/下载/t$ tar zvxf alltxt 
1.txt
2.txt
3.txt
4.txt
5.txt
6.txt
xxx@xxx-virtual-machine:~/下载/t$ ls
1.txt  2.txt  3.txt  4.txt  5.txt  6.txt  alltxt
```

**为了区分压缩包类型，压缩文件的时候，压缩包名字加上后缀 .tar.gz  .tar.bz**

```
xxx@xxx-virtual-machine:~/下载/t$ tar zvcf alltxt2.tar.gz *.txt
1.txt
2.txt
3.txt
4.txt
5.txt
6.txt
xxx@xxx-virtual-machine:~/下载/t$ ls
1.txt  2.txt  3.txt  4.txt  5.txt  6.txt  alltxt  alltxt2.tar.gz
```

##### rar

参数

压缩 a

解压 x 

不需要指定文件后缀了 会自动生成 .rar

**rar  a  压缩文件名  被压缩文件  压缩目录**

压缩

```
xxx@xxx-virtual-machine:~/下载/t$ rar a all  *.txt


Adding    1.txt                                                       OK 
Adding    2.txt                                                       OK 
Adding    3.txt                                                       OK 
Adding    4.txt                                                       OK 
Adding    5.txt                                                       OK 
Adding    6.txt                                                       OK 
Done
xxx@xxx-virtual-machine:~/下载/t$ ls
1.txt  2.txt  3.txt  4.txt  5.txt  6.txt  all.rar

```

解压到  ee文件夹

```
xxx@xxx-virtual-machine:~/下载/t$ ls
1.txt  2.txt  3.txt  4.txt  5.txt  6.txt  all.rar
xxx@xxx-virtual-machine:~/下载/t$ mkdir ee
xxx@xxx-virtual-machine:~/下载/t$ ls
1.txt  2.txt  3.txt  4.txt  5.txt  6.txt  all.rar  ee
```



```

xxx@xxx-virtual-machine:~/下载/t$ rar x all.rar -C ee

RAR 5.50   Copyright (c) 1993-2017 Alexander Roshal   11 Aug 2017
Trial version             Type 'rar -?' for help


Extracting from all.rar

Extracting  ee/1.txt                                                  OK 
Extracting  ee/2.txt                                                  OK 
Extracting  ee/3.txt                                                  OK 
Extracting  ee/4.txt                                                  OK 
Extracting  ee/5.txt                                                  OK 
Extracting  ee/6.txt                                                  OK 
All OK

```

```
xxx@xxx-virtual-machine:~/下载/t$ ls ee
1.txt  2.txt  3.txt  4.txt  5.txt  6.txt
```

##### zip

参数

压缩目录需要加   -r 

zip  压缩包名字   

unzip 压缩包名字  -d  解压到的目录 

**他们几个的语法都一样**

```
**tar/zip/rar   参数 (zvcf/ jvcf /   a    )  生成的压缩文件的名字    压缩的文件或目录** -C 压缩到的路径

**tar/unzip/rar   参数（zvxf /jvxf  /  x   ）   压缩文件的名字  参数（rar没有参数）-C / -d  解压到的目录 
```

## 进程管理

**who命令** 是显示目前登录系统的用户信息。执行who命令可得知目前有那些用户登入系统，单独执行who命令会列出登入帐号，使用的终端机，登入时间以及从何处登入或正在使用哪个X显示器

```
xxx@xxx-virtual-machine:/home$ who
xxx      pts/0        2022-06-20 07:25 (192.168.211.1)
xxx      tty2         2022-06-19 20:49 (tty2)
xxx@xxx-virtual-machine:/home$ 
```

总共有七个终端 

tty1 -tty 6 是文字终端     tty7是界面终端

通过 ctrl +alt +F1...F7  进行切换

pts 是设备终端 这里是   是x-shell的黑窗口

------



##### **查看进程**

ps -axu    查看没有终端的应用程序    （加了x 参数，  tty是问号）

```
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.5 166940 11052 ?        Ss   6月19   0:04 /sbin/init splash
root           2  0.0  0.0      0     0 ?        S    6月19   0:00 [kthreadd]
root           3  0.0  0.0      0     0 ?        I<   6月19   0:00 [rcu_gp]
root           4  0.0  0.0      0     0 ?        I<   6月19   0:00 [rcu_par_gp]
root           5  0.0  0.0      0     0 ?        I<   6月19   0:00 [netns]
root           7  0.0  0.0      0     0 ?        I<   6月19   0:00 [kworker/0:0H-events_highpri]
root          10  0.0  0.0      0     0 ?        I<   6月19   0:00 [mm_percpu_wq]
root          11  0.0  0.0      0     0 ?        S    6月19   0:00 [rcu_tasks_rude_]
root          12  0.0  0.0      0     0 ?        S    6月19   0:00 [rcu_tasks_trace]
root          13  0.0  0.0      0     0 ?        S    6月19   0:00 [ksoftirqd/0]
root          14  0.0  0.0      0     0 ?        I    6月19   0:23 [rcu_sched]
root          15  0.0  0.0      0     0 ?        S    6月19   0:00 [migration/0]
root          16  0.0  0.0      0     0 ?        S    6月19   0:00 [idle_inject/0]
root          17  0.0  0.0      0     0 ?        S    6月19   0:00 [cpuhp/0]
root          18  0.0  0.0      0     0 ?        S    6月19   0:00 [cpuhp/1]
root          19  0.0  0.0      0     0 ?        S    6月19   0:00 [idle_inject/1]
root          20  0.0  0.0      0     0 ?        S    6月19   0:00 [migration/1]
root          21  0.0  0.0      0     0 ?        S    6月19   0:00 [ksoftirqd/1]

```

我们执行查询进程的这个动作 也会差生一个进程（系统原本是不存在一个叫aaaaaaaaa的进程的）

```
xxx@xxx-virtual-machine:/home$ ps axu | grep aaaaaaaaa
xxx         9899  0.0  0.1  17748  2304 pts/0    S+   09:08   0:00 grep --color=auto aaaaaaaaa
xxx@xxx-virtual-machine:/home$ 
```

**所以查询结果应该是两条以上才算查询到结果**

##### **杀进程**

ps -9 pid (进程号)

-9是 SIGKILL 的序号

```
virtual-machine:/home$ kill -l
 1) SIGHUP	 2) SIGINT	 3) SIGQUIT	 4) SIGILL	 5) SIGTRAP
 6) SIGABRT	 7) SIGBUS	 8) SIGFPE	 9) SIGKILL	10) SIGUSR1
11) SIGSEGV	12) SIGUSR2	13) SIGPIPE	14) SIGALRM	15) SIGTERM
16) SIGSTKFLT	17) SIGCHLD	18) SIGCONT	19) SIGSTOP	20) SIGTSTP
21) SIGTTIN	22) SIGTTOU	23) SIGURG	24) SIGXCPU	25) SIGXFSZ
26) SIGVTALRM	27) SIGPROF	28) SIGWINCH	29) SIGIO	30) SIGPWR
31) SIGSYS	34) SIGRTMIN	35) SIGRTMIN+1	36) SIGRTMIN+2	37) SIGRTMIN+3
38) SIGRTMIN+4	39) SIGRTMIN+5	40) SIGRTMIN+6	41) SIGRTMIN+7	42) SIGRTMIN+8
43) SIGRTMIN+9	44) SIGRTMIN+10	45) SIGRTMIN+11	46) SIGRTMIN+12	47) SIGRTMIN+13
48) SIGRTMIN+14	49) SIGRTMIN+15	50) SIGRTMAX-14	51) SIGRTMAX-13	52) SIGRTMAX-12
53) SIGRTMAX-11	54) SIGRTMAX-10	55) SIGRTMAX-9	56) SIGRTMAX-8	57) SIGRTMAX-7
58) SIGRTMAX-6	59) SIGRTMAX-5	60) SIGRTMAX-4	61) SIGRTMAX-3	62) SIGRTMAX-2
63) SIGRTMAX-1	64) SIGRTMAX	
```

查看环境变量

##### env

##### (查看当前进程环境变量)

```
xxx@xxx-virtual-machine:/home$ env
SHELL=/bin/bash
LANGUAGE=zh_CN:zh
PWD=/home
LOGNAME=xxx
XDG_SESSION_TYPE=tty
MOTD_SHOWN=pam
HOME=/home/xxx
LANG=zh_CN.UTF-8
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.webp=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:
SSH_CONNECTION=192.168.211.1 50164 192.168.211.128 22
LESSCLOSE=/usr/bin/lesspipe %s %s
XDG_SESSION_CLASS=user
TERM=xterm
LESSOPEN=| /usr/bin/lesspipe %s
USER=xxx
DISPLAY=localhost:10.0
SHLVL=1
XDG_SESSION_ID=31
XDG_RUNTIME_DIR=/run/user/1000
SSH_CLIENT=192.168.211.1 50164 22
XDG_DATA_DIRS=/usr/share/gnome:/usr/local/share:/usr/share:/var/lib/snapd/desktop
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus
SSH_TTY=/dev/pts/0
_=/usr/bin/env
OLDPWD=/home/xxx

```

注意这里的环境变量是键值对   通过  ： 分割

key =value : value : value :value :value 

```
xxx@xxx-virtual-machine:/home$ env | grep PATH
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin

```

##### top (相当于任务管理器)

```shell
 进程号 USER      PR  NI    VIRT    RES    SHR    %CPU  %MEM     TIME+ COMMAND                                    
    594 root      20   0  326444   7632   6600 S   0.3   0.4   2:43.33 vmtoolsd                                   
    769 root      20   0 1317912  20620   6684 S   0.3   1.1   0:06.89 snapd                                      
   1274 root      20   0   82144   6536   5412 S   0.3   0.3   1:16.06 vmtoolsd                                   
      1 root      20   0  166940  11052   7568 S   0.0   0.6   0:04.61 systemd                                    
      2 root      20   0       0      0      0 S   0.0   0.0   0:00.07 kthreadd                                   
      3 root       0 -20       0      0      0 I   0.0   0.0   0:00.00 rcu_gp            
```

nslookup (通过域名得到Ip)

```
xxx@xxx-virtual-machine:/home$ nslookup www.taobao.com
Server:		127.0.0.53
Address:	127.0.0.53#53

** server can't find www.taobao.com: SERVFAIL
```

## 用户管理

##### **增加用户**（方式一 推荐）

adduser  zzz

```
xxx@xxx-virtual-machine:/home$ sudo adduser zzz
正在添加用户"zzz"...
正在添加新组"zzz" (1001)...
正在添加新用户"zzz" (1001) 到组"zzz"...
创建主目录"/home/zzz"...
正在从"/etc/skel"复制文件...
新的 密码： 
```

会先创建组  组名和用户名相同

##### 切换 用户

su zzz(用户名)

##### 方式二 

useradd  -s    bin/bash  -g ccc  -d  /home/ccc   -m ccc

参数  -s 指定 shell   -g 指定组    -d 指定用户主目录     -m 如果家目录下没有ccc 就创建一个

```
xxx@xxx-virtual-machine:/home$ useradd -s /bin/bash -g ccc1 -d /home/ccc2  -m ccc3
useradd：“ccc1”组不存在
```

注意这里要先创建一个组。

```
xxx@xxx-virtual-machine:/home$ sudo groupadd ccc1 
xxx@xxx-virtual-machine:/home$ sudo useradd -s /bin/bash -g ccc1 -d /home/ccc2  -m ccc3
xxx@xxx-virtual-machine:/home$ ls
ccc2  Robin  xxx  zzz
```



但是我们指定的家目录如果没有的话是创建 ccc3 呐 

原因    useradd默认添加了-m的参数  

```
man useradd就可以看到如此介绍：Create the user´s home directory if it does not exist.(它将创建用户的主目录即使不存在)

接着下面useradd will create the home directory unless CREATE_HOME in
/etc/login.defs is set to no.
这里应该看懂了吧，假如在/etc/login.defs中的CREATE_HOME被设置为no那么只输入useradd将不能创建用户主目录 
```

使用这种方式创建的用户是 ccc3

比如

```
xxx@xxx-virtual-machine:/home$ sudo useradd -s /bin/bash -g tt -d /home/tt2  -m tt3
xxx@xxx-virtual-machine:/home$ ls
Robin  tt2  xxx  zzz
```

```
xxx@xxx-virtual-machine:/home$ sudo passwd tt
passwd：用户“tt”不存在
xxx@xxx-virtual-machine:/home$ sudo passwd tt2
passwd：用户“tt2”不存在
xxx@xxx-virtual-machine:/home$ sudo passwd tt3
新的 密码： 
无效的密码： 密码少于 8 个字符
重新输入新的 密码： 
passwd：已成功更新密码
xxx@xxx-virtual-machine:/home$ su tt3
密码： 
tt3@xxx-virtual-machine:/home$ whoami
tt3
```

**注意::::::**   **-m 指定的是 tt3  所以创建的用户是 tt3**

虽然  /home目录下是 tt2

```
tt3@xxx-virtual-machine:/home$ ls
Robin  tt2  xxx  zzz
```



这一点也可以使用  cat   etc/passwd 验证

##### **查看所有用户**

```
cat /etc/passwd
xiao:x:1004:1004::/home/xiao:/bin/sh
Robin:x:1005:1005::/home/Robin:/bin/bash
ccc3:x:1006:1006::/home/ccc2:/bin/bash

```



##### 给指定用户指定密码

```
root@xxx-virtual-machine:/home# sudo passwd zzz
新的 密码： 
无效的密码： 密码是一个回文
重新输入新的 密码： 
passwd：已成功更新密码
root@xxx-virtual-machine:/home# 
```

##### 退出用户

exit

##### 用户删除

##### 方式一

deluser  tt3

```
xxx@xxx-virtual-machine:/home$ sudo deluser tt3 
[sudo] xxx 的密码： 
对不起，请重试。
[sudo] xxx 的密码： 
正在删除用户 'tt3'...
警告：组"tt"没有其他成员了。
完成。
xxx@xxx-virtual-machine:/home$ ls
Robin  tt2  xxx  zzz
xxx@xxx-virtual-machine:/home$ rm -rf tt2
rm: 无法删除 'tt2': 权限不够
xxx@xxx-virtual-machine:/home$ sudo rm -rf tt2
```

虽然用户没有了，但是用户的家目录还在，所以还要删除家目录

```
xxx@xxx-virtual-machine:/home$ ls
Robin  xxx  zzz
```

##### **方式二(推荐)**

userdel    zzz  -r   

```
xxx@xxx-virtual-machine:/home$ sudo userdel zzz -r
userdel：zzz 信件池 (/var/mail/zzz) 未找到
xxx@xxx-virtual-machine:/home$ ls
Robin  xxx
```

# **搭建Ftp**     

（用于文件上传和下载） (使用 vsftpd   )

## **服务端配置**

1> 修改配置文件

```
vim etc/vsftpd.conf
```

```
anonymous_enable=YES          #允许匿名用户登录
local_enable=YES 
write_enable=YES              #是否拥有写权限
local_umask=022               #设置本地掩码
anon_upload_enable=YES        # 匿名用户上传权限
anon_mkdir_write_enable=YES   # 匿名用户创建文件夹权限
dirmessage_enable=YES
use_localtime=YES
xferlog_enable=YES
port 20 (ftp-data).
connect_from_port_20=YES

```



2>修改配置文件之后  需要重启服务

```
sudo service vsftpd restart
```



## **客户端**---实名用户登录****

查看本地用户。 

```
xxx@xxx-virtual-machine:~$ cd /home
xxx@xxx-virtual-machine:/home$ ls
Robin  xxx  zzz
```

以xxx 作为 客户端  以 登录的用户作为服务端

```
ftp 服务端ip
```

服务端ip   使用 ifconfig

```
  inet 192.168.211.128  netmask 255.255.255.0  broadcast 192.168.211.255
```

```
xxx@xxx-virtual-machine:/home$ ftp 192.168.211.128
Connected to 192.168.211.128.
220 (vsFTPd 3.0.5)
Name (192.168.211.128:xxx): zzz
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> 
```

**以 zzz 用户 身份登录ftp服务器，进入的是zzz 家目录**

```
ftp> pwd
Remote directory: /home/zzz
```

退出  zzz  登录

```
ftp> bye
221 Goodbye.
```

切换 Robin 登录ftp服务器

```
xxx@xxx-virtual-machine:/home$ ftp 192.168.211.128
Connected to 192.168.211.128.
220 (vsFTPd 3.0.5)
Name (192.168.211.128:xxx): Robin
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp>
```

进入的就是Robin的家目录

```
ftp> pwd
Remote directory: /home/Robin
```

------

##### **文件的上传**

 **只支持传送普通文件，不能上传目录**   

使用 get  文件名

**在 客户端的哪个文件夹登录ftp   就只能  上传哪个文件夹的文件   或下载 文件到哪个文件夹  。**

 在	xxx 用户的	 test 文件夹中登录    （其中 p 是目录）

```
xxx@xxx-virtual-machine:~/test$ ls
1.txt  2.txt  3.txt  p
xxx@xxx-virtual-machine:~/test$ ftp 192.168.211.128
```

以  zzz 用户登录

```
Name (192.168.211.128:xxx): zzz
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> 
```

如果向 zzz 服务器 传送 一个  在 xxx客户端的test目录上不存在的 4.txt

```
local: 4.txt remote: 4.txt
ftp: Can't open `4.txt': 没有那个文件或目录
```

如果传送一个目录 （无法传送目录）

```
local: p remote: p
p: not a plain file.
```

##### **文件的下载**   

**文件也只会下载到 test 文件夹**

```
get 文件名
```

现在服务端的文件有  （其中 1.txt  3.txt是上传的）  

```
ftp> ls
229 Entering Extended Passive Mode (|||6434|)
150 Here comes the directory listing.
-rw-r--r--    1 1001     1001            0 Jun 20 19:53 1.txt
-rw-r--r--    1 1001     1001            0 Jun 20 19:53 3.txt
-rw-r--r--    1 0        0               0 Jun 20 20:08 testfile
```

要下载 testfile

```
ftp> get testfile
local: testfile remote: testfile
229 Entering Extended Passive Mode (|||49825|)
150 Opening BINARY mode data connection for testfile (0 bytes).
     0        0.00 KiB/s 
226 Transfer complete.
```

切回 xxx用户的test目录  （传送成功）

```
xxx@xxx-virtual-machine:~/test$ ls
1.txt  2.txt  3.txt  p  testfile
```

## 客户端-----**匿名登录服务器**

相较于实名登录 ，匿名登录可以 限制登录用户的访问（创建匿名用户的根目录）

在ftp服务器 （假设就是 zzz 去登录）上创建匿名用户根目录

##### 创建Ftp作为  根目录

```
.
├── 1.txt
├── 2.txt
├── 3.txt
├── 4.txt
├── 5.txt
├── 6.txt
└── Ftp
    ├── a
    └── b
```



同时修改 配置文件

```
vim /etc/vsftpd.conf
anon_root=/home/zzz/Ftp     //配置文件中加上这句
```

重启 vsftpd 服务

```
sudo service vsftpd restart
```

在 客户端（xxx）的test 目录中登录

注意 登录Name  一定是  anonymous     密码 直接回车

```
xxx@xxx-virtual-machine:~/test$ ls
5.txt  6.txt  p
xxx@xxx-virtual-machine:~/test$ ftp 192.168.211.128
Connected to 192.168.211.128.
220 (vsFTPd 3.0.5)
Name (192.168.211.128:xxx): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> 

```

查看当前目录 （确实是在根目录）

```
ftp> pwd
Remote directory: /
```

文件也是之前设置的匿名用户目录

```
ftp> ls
229 Entering Extended Passive Mode (|||63988|)
150 Here comes the directory listing.
drwxr-xr-x    2 0        0            4096 Jun 20 21:05 a
drwxr-xr-x    2 0        0            4096 Jun 20 21:05 b
226 Directory send OK.
```

##### 上传文件  

 put 文件名

```
226 Directory send OK.
ftp> put 5.txt
local: 5.txt remote: 5.txt
229 Entering Extended Passive Mode (|||28113|)
553 Could not create file.
```

如果发现不能创建 ，就去改变服务端文件夹的权限

```
root@xxx-virtual-machine:/home/zzz/Ftp# cd ../
root@xxx-virtual-machine:/home/zzz# chmod 777 Ftp
```

上传成功

```
ftp> put 5.txt
local: 5.txt remote: 5.txt
229 Entering Extended Passive Mode (|||14064|)
150 Ok to send data.
     0        0.00 KiB/s 
226 Transfer complete.
```

##### 下载文件 

get 文件名

```
ftp> ls
229 Entering Extended Passive Mode (|||34463|)
150 Here comes the directory listing.
-rw-------    1 131      136             0 Jun 20 22:52 5.txt
drwxrwxrwx    2 0        0            4096 Jun 20 21:05 a
drwxrwxrwx    2 0        0            4096 Jun 20 21:05 b
-rwxrwxrwx    1 0        0               0 Jun 20 23:21 c
226 Directory send OK.
ftp> get c
local: c remote: c
229 Entering Extended Passive Mode (|||43218|)
150 Opening BINARY mode data connection for c (0 bytes).
     0        0.00 KiB/s 
226 Transfer complete.
```

## **lFtp工具的使用**

lftp 可以切换客户端目录 （意味着，我们可以不再遵循   **在 客户端的哪个文件夹登录ftp   就只能  上传哪个文件夹的文件   或下载 文件到哪个文件夹  。**），       也可以上传或下载目录

也支持批量上传和批量下载



工具安装

```
sudo apt-get install lftp
```



### 匿名登录

#### 前期准备

同样要创建匿名用户根目录(Ftp)（和ftp 匿名登录设置相同）



**预先在服务端 Ftp 目录下准备一些文件**

```
dr-xr-xr-x 4 root root 4096  6月 21 09:42 ./
drwxr-x--- 3 zzz  zzz  4096  6月 21 09:38 ../
-rwxrwxrwx 1 root root    0  6月 21 09:41 1*
-rwxrwxrwx 1 root root    0  6月 21 09:41 2*
-rwxrwxrwx 1 root root    0  6月 21 09:41 3*
drwxrwxrwx 2 root root 4096  6月 21 09:42 t/
drwxrwxrwx 2 root root 4096  6月 21 09:42 tt/
```

注意 我将 Ftp (也就是 . 当前目录)的权限设置为 555 ，因为不能给 Ftp 根目录设置写权限。

（如果遇到问题改访问权限就好了）

客户端准备 test (用于上传) test2（作为下载目录） 文件夹  ，同时在test 文件夹里创建   文件夹和文件     。 以便演示 客户端目录切换

```

root@xxx-virtual-machine:/home/xxx# tree test -lhx

[4.0K]  test
├── [4.0K]  p1      目录
├── [4.0K]  p2	    目录
├── [4.0K]  p3      目录
├── [   0]  testfile1         文件
├── [   0]  testfile2	      文件		
├── [   0]  testfile3          文件
└── [   0]  testfile4          文件
```

#### 登录

在xxx的test 文件夹下登录

```
root@xxx-virtual-machine:/home/xxx/test# lftp 192.168.211.128
lftp 192.168.211.128:~> login
用法：login <user|URL> [<pass>]
lftp 192.168.211.128:~> ls
-rwxrwxrwx    1 0        0               0 Jun 21 09:41 1
-rwxrwxrwx    1 0        0               0 Jun 21 09:41 2
-rwxrwxrwx    1 0        0               0 Jun 21 09:41 3
drwxrwxrwx    2 0        0            4096 Jun 21 09:42 t
drwxrwxrwx    2 0        0            4096 Jun 21 09:42 tt
```

查看当前客户端的路径

```
lpwd
```

查看当前服务端路径

```
pwd
```

切换服务端目录

```
cd
```

切换客户端目录

```
lcd
```

#### **下载**

下载  1  到  test2

批量下载 2 3 到   test2

```
lftp 192.168.211.128:/t> lcd ../test2           #切换目录
lcd 成功，本地目录=/home/xxx/test2


xxx@xxx-virtual-machine:~/test2$ lftp 192.168.211.128     # ftp 变成了 lftp
lftp 192.168.211.128:~> lpwd                               #客户端  xxx 当前的目录
/home/xxx/test2
lftp 192.168.211.128:~> get 1
lftp 192.168.211.128:/> mget 2 3                             
总共传输 2 个文件
lftp 192.168.211.128:/> pwd                                 # 服务端当前的目录
ftp://192.168.211.128/
```

下载成功

```
root@xxx-virtual-machine:/home/xxx/test2# ls
1  2  3
```

 下载目录

```
mirror   目录名
```

```
lftp 192.168.211.128:/> mirror t
lftp 192.168.211.128:/> mirror tt
```

下载成功

```
root@xxx-virtual-machine:/home/xxx/test2# ls
1  2  3  t  tt

```

切换到客户端的  test 上传文件和目录

#### 上传目录

```
mirror  -R   目录
```

上传目录遇到的错误

```
lftp 192.168.211.128:/> mirror pj  -r
mirror: 访问失败: 550 Failed to change directory. (/pj)
```

原因 把 -r  改成  -R

```
lftp 192.168.211.128:/> mirror pj  -R
全部：2 个目录，0 个文件，0 个符号链接
```

上传文件出现错误

```
lftp 192.168.211.128:/> put files
put: 访问失败: 553 Could not create file. (files)
```

解决方式 ： 把匿名用户根目录改为 777 权限

但此时无法使用 get 下载文件， 所以下载文件时 把 Ftp 目录权限 改回 555



# nfs 服务器使用



### **服务端**

#### **创建共享目录（以zzz 为客户端）**

创建了一些测试文件

```
root@xxx-virtual-machine:/home/zzz# mkdir nfsShare
root@xxx-virtual-machine:/home/zzz# cd  nfsShare
root@xxx-virtual-machine:/home/zzz/nfsShare# touch {1..6}
root@xxx-virtual-machine:/home/zzz/nfsShare# mkdir test
```

#### **修改配置文件**

```
 sudo vim /etc/exports
```

配置文件内容

```
/home/zzz/nfsShare/ *(rw,nohide,insecure,no_subtree_check,async,no_root_squash)
/home/zzz/nfsShare  *(rw,nohide,insecure,no_subtree_check,async,no_root_squash)
/home/zzz/nfsShare *(rw,nohide,insecure,no_subtree_check,async,no_root_squash)

```

前面为想挂载的目录
*：允许所有的网段访问，也可以使用具体的IP
rw：挂载此目录的客户端对该共享目录具有可读可写权限
sync：资料同步写入内存和硬盘
no_root_squash：root用户具有对根目录的完全管理访问权限
no_subtree_check：不检查父目录的权限

#### **让exports文件生效 （原来缺了这一步）**

```
sudo exportfs -arv
```

#### **重启服务**

```
sudo /etc/init.d/nfs-kernel-server restart
```

### 客户端 （xxx作为客户端）

#### **挂载服务器共享目录**

mount serverIp : 共享文件夹    /mnt

```
root@xxx-virtual-machine:/home/xxx# mount 192.168.211.128:/home/zzz/nfsShare /mnt

```

#### 挂载成功

```
root@xxx-virtual-machine:/home/xxx# tree /mnt
/mnt
├── 1
├── 2
├── 3
├── 4
├── 5
├── 6
└── test
```

# 搭载SSH**服务器**



以 xxx 为 服务端  ，zzz用户  远程登录到xxx 所在的目录

```
zzz@xxx-virtual-machine:/home/Robin$ sudo su
[sudo] zzz 的密码： 
```

格式  ：    ssh 用户名@serverIp 

```
zzz@xxx-virtual-machine:~$ ssh xxx@192.168.211.128
```

```
The authenticity of host '192.168.211.128 (192.168.211.128)' can't be established.
ED25519 key fingerprint is SHA256:wCYFwPuDeglP0uGMt42XKdfrJZaqW+xbem1ifW/p5xQ.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.211.128' (ED25519) to the list of known hosts.
xxx@192.168.211.128's password: 
Welcome to Ubuntu 22.04 LTS (GNU/Linux 5.15.0-39-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

0 updates can be applied immediately.

*** System restart required ***
Last login: Tue Jun 21 21:44:25 2022 from 192.168.211.128

```

可以访问到 xxx 用户的目录 

```
xxx@xxx-virtual-machine:~$ ls
11  公共的  模板  视频  数据库实验排序版  图片  文档  下载  音乐  桌面  snap
```

##### **退出登录**

```
xxx@xxx-virtual-machine:~$ logout
Connection to 192.168.211.128 closed.

```



# SCP命令

超级拷贝 ，相比于 cp 只能在同一个用户的 目录 操作。

SCP 支持 两个用户 间 文件或目录的拷贝

格式

```
scp -r目标用户名@目标主机IP地址:/目标文件的绝对路径		/保存到本机的绝对(相对)路径
```



```
xxx@xxx-virtual-machine:~$ scp zzz@192.168.211.128:/home/zzz/testfile  /home/xxx
zzz@192.168.211.128's password: 
Permission denied, please try again.
zzz@192.168.211.128's password: 
testfile                                                                         100%    0     0.0KB/s   00:00    
```

```
xxx@xxx-virtual-machine:~$ scp -r  zzz@192.168.211.128:/home/zzz/p_testfile   /home/xxx
zzz@192.168.211.128's password: 
xxx@xxx-virtual-machine:~$ ls
11  公共的  模板  视频  数据库实验排序版  图片  文档  下载  音乐  桌面  p_testfile  snap  testfile
```

清屏快捷键

```
ctrl + l
```



# Vim 快捷键

a 在光标后插入

i  在光标前插入

o 下一行插入

O上一行插入

dd  剪切 		p  粘贴 			x 删除当前光标所覆盖的字体。 X 删除光标前面的字体

数字0 回到 行首   ，$ 回到行尾

shift +d  删除光标后所有行

d +0 删除光标前所有行

删除多行  n行 +dd

复制多行  n行 +yy

跳到文件最末尾 GG  ,最开始处 gg

行跳转     n 行 G

撤销 u  

反撤销   Ctrl  +r

#### **查找**

命令模式下  敲   /   

输入查找字符 回车      按 n 向下搜索 ，按N 向上搜索 

### 替换模式

按 R切换 到替换模式

可视模式   

按  v     进入 可以配合 h j k l 使用 



shift + v   选中一行

ctrl + v 选中一列

### **vim 的分屏**

：sp   水平分屏 

Ctrl ww  切换上下屏

：wall 保存所有

：qall 退出所有

：vsp 垂直分屏

：vsp  文件名  垂直分屏打开另外一个文件

# **gcc 编译过程**

ESc  -----  iso  

  gcc -E  test.c -o test.i   展开头文件，宏替换 ，去掉注释

gcc -S test.i -o hello.s  变成汇编文件 

 汇编文件变成二进制文件 gcc -c  test.s test.o


函数库的相应代码组合到目标文件中  gcc test.o  -o  test 

参考我的https://blog.csdn.net/qq_55125921/article/details/123973900

gcc 的一些参数

```
- I 指定头文件
- D 指定宏（包含这个宏）
- O3 优化
-Wall  提示信息
-g 添加调试信息
```

 当前文件结构。

```
xxx@xxx-virtual-machine:~/t$ tree 
.
├── Cfile
│   ├── add.c
│   ├── main.c
│   ├── mul.c
│   └── sub.c
└── Include
    ├── add.h
    ├── mul.h
    └── sub.h

```

sub.c

```
#include"sub.h"
int sub (int x,int y)
{
return x-y; 
}
```

执行语句是  （头文件不和 .c 文件在同一个目录时,用  -I 指定 ）

```
gcc sub.c add.c  mul.c  main.c  -I ../Include/ -o testfile
```



main.c 

```c
#include "add.h"
#include "sub.h"
#include "mul.h"
#include<stdio.h>
#define Debug
int main()
{

int x=2;
int y=4;

 printf("%d",add(x,y));
 printf("\n");
printf("%d",sub(x,y));

 printf("\n");
printf("%d",mul(x,y));

 printf("\n");
printf("hello");

 printf("\n");
#ifdef Debug
 
printf("注释语句")
  ;
#endif

return 0;]

}
```

main.c的注释语句 是在包含宏的情况下才打印的   ，但如果多个文件含有注释语句 ，那头文件包含宏 就得写多次  ，所以我们一般在使用 gcc 的时候 -D 指定，此时就可以去掉 #define  Debug.

注释掉main.c的#define  Debug

```
xxx@xxx-virtual-machine:~/t/Cfile$ gcc sub.c add.c  mul.c  main.c  -I ../Include/ -o testfile
xxx@xxx-virtual-machine:~/t/Cfile$ ./testfile
6
-2
8
hello

```

```
xxx@xxx-virtual-machine:~/t/Cfile$ gcc sub.c add.c  mul.c  main.c  -I ../Include/ -o testfile -D Debug
xxx@xxx-virtual-machine:~/t/Cfile$ ./testfile
6
-2
8
hello
注释语句xxx@xxx-virtual-machine:~/t/Cfile$ 
```



### **静态库的制作** 

先利用头文件和c 文件  生成 目标文件。

将目标文件生成库  

```
xxx@xxx-virtual-machine:~/t/src$ gcc *.c -c -I ../Include 
xxx@xxx-virtual-machine:~/t/src$ ls
add.c  add.o  mul.c  mul.o  sub.c  sub.o
```

```
xxx@xxx-virtual-machine:~/t/src$ ar rcs libMy.a  *.o
xxx@xxx-virtual-machine:~/t/src$ ls
add.c  add.o  libMy.a  mul.c  mul.o  sub.c  sub.o
```

语法格式

```
ar rcs   lib动态库名字.a   *.o
```

可以使用 nm 命令查看 库文件和可执行文件的内容

```
xxx@xxx-virtual-machine:~/t/lib$ nm libMy.a

add.o:
0000000000000000 T add

mul.o:
0000000000000000 T mul

sub.o:
0000000000000000 T sub
xxx@xxx-virtual-machine:~/t/lib$ 

```



### **gcc 使用静态库**

发布时给客户  main.c   Include 中的头文件  还有库文件就可以了

静态库需要  掐头去尾  （lib 和  .a  后缀 都需要去掉）

```
xxx@xxx-virtual-machine:~/t$ gcc main.c -I - -L lib  -l My -o  myapp
```

```
-L  指定 静态库目录  
-l  指定使用哪个静态库
-o 指定生成可执行文件的名字
```

### **静态库缺点**

```
静态库的优点：
1.
发布程序的时候,
不需要提供对应的库
2.
加载库的速度快
缺点：
1.
库被打包到应用程序中，导致库的体积很大
2.
库发生了改变，需要重新编译程序

```

### 动态库制作

1 生成于位置无关的代码

```
xxx@xxx-virtual-machine:~/t/src$ gcc -fPIC  -c  *.c  -I ../Include -D Debug
xxx@xxx-virtual-machine:~/t/src$ ls
add.c  add.o  mul.c  mul.o  sub.c  sub.o
```

2 生成动态库

```
xxx@xxx-virtual-machine:~/t/src$ gcc -shared -o libMy.so *.o 

```

语法格式

```
gcc -shared *.o  -o lib动态库名字.so
```

#### **使用动态库**

main.c     和  动态库 生成可执行文件

```
gcc main.c -I Include -L lib  -l My -o  app -D Debug
```

**执行文件**

```
./app: error while loading shared libraries: libMyShare.so: cannot open shared object file: No such file or directory
```

使用  ldd app     查看动态库链接情况

```
xxx@xxx-virtual-machine:~/t$ ldd app
	linux-vdso.so.1 (0x00007ffeec79e000)
	libMyShare.so => not found
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f69e5753000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f69e5992000)
```

#### 解决动态链接文件出错的问题

不推荐

```
把库文件放进   lib
cp  libMyShare   /lib
```

##### **方式二  （临时）** 

存放动态库的环境变量 ，并将环境变量导入系统环境变量

```
export LD_LIBRARY_PATH = ./lib 
```

./lib 是你库文件存放的地方

#####  **方法三 （永久）**

在家目录的 .bashrc 文件中添加 方法二的

```
export LD_LIBRARY_PATH = 动态库绝对路径
```

 重启终端就可以了





##### 方式四   （推荐）



**动态库绝对路径    写入配置文件中 **

 找到配置文件

```
cd  /etc/ld.so.conf
```

写入动态库的绝对路径

```
/home/xxx/t/lib

```

更新配置文件

```
sudo ldconfig -v
```

ldd 可执行文件   查看链接的动态库

```
	libMyShare.so => /home/xxx/t/lib/libMyShare.so (0x00007ff1b49e5000)

```

方法五 创建符号链接

```
ln -s 动态库绝对路径  动态库名字 
```



