##  Linux    系统函数

可以使用  man 2 函数名 查看 系统函数

也可以在 文件内 使用  2+ shift +k  进入指定函数 （前提是光标停在指定函数上）

## open 函数

```
  int open(const char *pathname, int flags);
  int open(const char *pathname, int flags, mode_t mode);     
```

### open打开文件

```c
       #include <sys/types.h>
       #include <sys/stat.h>
       #include <fcntl.h>  //以上三个是open的头文件
#include<unistd.h>//close   unix的标准头文件
#include<stdlib.h>//exit
#include<stdio.h>//perror
int main()
{
//使用open函数打开文件
int fd ; //文件描述符号
fd=open("hello.c",O_RDWR);//返回文件表识符
if (fd==-1)
{
perror("open file--");//括号中的内容是自己指定的，perror是全局变量，当打开文件失败，调用这个函数会得到错误信息
exit(1);
}

int ret=close(fd);
printf("ret=%d\n",ret);
//当关闭文件失败会返回-1  (正确会返回0) ，用perror打印错误信息
if(ret==-1)
{
perror("close file--");
exit(1);

}


}

```

### open 创建文件

```c
//open 创建新文件
fd=open("myhello",O_RDWR | O_CREAT, 0777);
if(fd ==-1)
{
perror("Create file");
exit(1);
}
printf("fd=%d\n",fd); 
int ret=close(fd);
printf("ret=%d\n",ret);

```

执行文件之后，创建的  myhello 权限并不是 777

```
-rwxrwxr-x 1 xxx xxx 0  7月  5 21:56 myhello*
```

实际的文件权限 是 给定权限 取反  再和本地掩码 相与的结果

可以使用 umask 临时指定本地掩码

```
umask  0022
```

### open 判断文件是否存在

多加了一个 O_EXCL

```c
fd=open("myhel",O_RDWR | O_CREAT|O_EXCL,0777);
  if(fd ==-1)
 {
  perror("IS EXIST ?");
  exit(1);
 }
  printf("fd=%d\n",fd);
```

### open 截断某个文件（清空文件）

```c
//将文件截断为0
fd=open("test.c",O_RDWR | O_TRUNC);
if(fd ==-1)
{
perror("IS TRUNC");
exit(1);
}
printf("fd=%d\n",fd); 
```



## read and write 函数

包含头文件和格式

```c
#include <unistd.h>
ssize_t read(int fd, void *buf, size_t count);   
```

如果fd 为 1 ，对应 stdout  输出文件到屏幕 

fd 为 0 ,对应 stdin  从屏幕读入内容到文件

fd 为 2  对应 stderror   

标准输入通常是值为0的文件描述符，标准输出通常是值为1的文件描述符，标准错误通常是值为2的文件描述符。
当前进程打开的其他文件将获得值3、4、5，依此类推。

文件描述符是小整数值，与文件或其他输入/输出资源(例如管道或网络套接字)相对应。文件描述符是资源的抽象指示符，并充当执行各种较低级别I /O操作(如读取，写入，发送等)的句柄

**read读取设备 时 默认会阻塞 ，比如读取管道信息， 会阻塞等待管道 中有数据 才会 读取。**

```c
#include<stdio.h>  
#include<stdlib.h>
#include<sys/types.h>
#include<sys/stat.h>
#include<fcntl.h>
#include<unistd.h>  // read_ write
int main()
{
//打开已经存在的文件  只读操作    (我们只需要读取其中的内容)
    int fd=open("1.txt",O_RDONLY);
    if(fd==-1)
    {
     perror ("open");
      exit(1);
    }
    //创建一个新文件  
   int fd1=open("newfile",O_CREAT | O_WRONLY, 0664);   //这个文件准备写入数据
    if(fd==-1)   
    {
      perror ("open1");
      exit(1);
    }
//读取文件

char buf[2048]={0};     //准备一个缓冲区
int count =read(fd,buf,sizeof(buf));    //把从1.txt文件中（用文件描述符 fd 指定） 读出的内容，保存到缓存区 buf 
if(count==-1)                           //read 函数 返回值是读出的 文件的字节数（大小）
{
    perror("read");                     //如果read()返回值是 -1 ，则 读取失败
    exit(1);
}    
while(count)   //当读出的数据  		
{
    int ret=write(fd1,buf,count);		// 根据文件描述符 将 缓冲区内的数据读取到  newfile 文件中
printf("write bytes %d\n",ret);
count =read(fd,buf,sizeof(buf));   	//重新赋值 count
}
close(fd);  		//关闭文件
close(fd1);
}
```



## lseek 函数

```
#include <sys/types.h>
#include <unistd.h>

off_t lseek(int fd, off_t offset, int whence);
当调用成功时则返回目前的读写位置，也就是距离文件开头多少个字节。若有错误则返回-1，errno 会存放错误代码。

函数设置文件指针stream的位置。如果执行成功，stream将指向以where为基准，偏移offset个字     节的位置。如果执行失败(比如offset超过文件自身大小)，则不改变stream指向的位置。
```



```
　SEEK_SET：表示文件的相对起始位置

　SEEK_CUR：表示文件的相对当前位置

　SEEK_END：表示文件的相对结束位置
```





### lseek 获取文件大小

### lseek 拓展文件大小 

```c
#include<stdio.h>
#include<stdlib.h>
#include<sys/types.h>
#include<sys/stat.h>
#include<fcntl.h>
#include<unistd.h>

int main()
{
int fd=open("aa",O_RDWR);
if(fd==-1)
{

    perror("open file");
    exit(1);
}
 int ret=lseek(fd,0,SEEK_END);//获取文件长度(大小)
printf("file length=%d\n",ret);
//
// 文件向后扩展 
 ret =lseek(fd,2000,SEEK_END);
printf("return value %d\n",ret);
//实现扩展，要额外进行一次写操作
write(fd,"a",1);
close(fd);
return 0;
}

```

### **lseek 移动文件指针**

```
1) 欲将读写位置移到文件开头时:
lseek（int fildes,0,SEEK_SET）；//  fd是文件描述符定位文件用
2) 欲将读写位置移到文件尾时:
lseek（int fildes，0,SEEK_END）；
3) 想要取得目前文件位置时:
lseek（int fildes，0,SEEK_CUR）；

当 whence 值为SEEK_CUR 或SEEK_END时，参数offet允许负值的出现。
```



```c
#include<stdio.h>
 //演示lseek的 文件指针移动
#include<sys/io.h> 
#include<fcntl.h>
#include<stdio.h>
#include<stdlib.h>
#include<sys/types.h>
#include<sys/stat.h>
#include<fcntl.h>
#include<unistd.h>
int main(void){
 int fd =0;
    fd=open("2.txt",O_RDONLY); //事先准备好一个2.txt文件准备打开
 
   if(fd==-1){
 
      printf("can not open the file\n");
 
      return 1;
 
   }
 int pos =(int)fd;
 
   printf("before lseek function,current position: %d\n",(int)lseek(fd,0,SEEK_SET));
 
   lseek(fd,7,SEEK_SET);  //移动到以文件的开头偏移7个字节的位置
 
   char buf[20]={"\n"};
 
   read(fd,buf,14);//读文件时，文件指针也会移动
 
   printf("the res is %s\n",buf);
 
   printf("after lseek function,current position: %d\n",(int)lseek(fd,0,SEEK_CUR));
 
 //这里注意，需要强制转换
 
   close(fd);
 
   return 0;
 
}

```

## ftruncate 函数  

和 fseek 函数类似

系统调用ftruncate可以将一个文件裁剪为指定的大小，函数描述如下：

头文件：<unistd.h> <sys/types.h>
函数使用：
int truncate(const char *path, off_t length);
int ftruncate(int fd, off_t length);
函数参数：
可以看到两者有不同的使用方式，truncate是通过文件路径来裁剪文件大小，而ftruncate是通过文件描述符进行裁剪；
返回值
成功：0
失败：-1
权限要求：
ftruncate要求文件被打开且拥有可写权限
truncate要求文件拥有可写权限
注意：
如果要裁剪的文件大小大于设置的off_t length,文件大于length的数据会被裁剪掉
如果要裁剪的文件小于设置的offt_t length，则会进行文件的扩展，并且将扩展的部分都设置为\0,文件的偏移地址不会发生变化
ftruncate主要被用作POSIX共享内存对象的大小设置

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

#define TRUN "./test_truncate"
int main() {
	char *name = "test_file";
	//打开文件且拥有可写权限
	int fd = open(name,O_CREAT|O_RDWR,0666);
	if ( -1 == fd ) {
		printf("open %s failed \n",name);
		_exit(-1);
	}
	
	//通过文件描述符对文件大小进行裁剪
	if(ftruncate(fd,4096) != -1) {
		printf("ftruncate success \n");
	}
	
	//直接设置指定路径的文件大小
	if(truncate(TRUN,8192) != -1) {
		printf("truncate success\n");
	}

	//通过fstat获取文件属性信息
	struct stat buf;
	fstat(fd,&buf);
	printf("stat.size is %ld \n",buf.st_size);
	printf("stat.inode is %ld \n",buf.st_ino);
	printf("stat.mode is %lo(octal) \n",(unsigned long)buf.st_mode);
	printf("stat.bytes is %ld \n",buf.st_blksize);
	close(fd);
	return 0;
}

```





## stat 函数（）

获取文件信息

man 2 stat 

依赖头文件

```
       #include <sys/types.h>
       #include <sys/stat.h>
       #include <unistd.h>
```

```
       int stat(const char *pathname, struct stat *statbuf);   
       int fstat(int fd, struct stat *statbuf);  
       int lstat(const char *pathname, struct stat *statbuf);
```

### 3个函数区别

stat 是穿透型的函数  当你用 stat 查看一个软链接 时，显示出的状态信息是 源文件的

```
fstat的第一个参数是和另外两个不一样的，对！fstat区别于另外两个系统调用的地方在于，fstat系统调用接受的是 一个“文件描述符”，而另外两个则直接接受“文件全路径”。文件描述符是需要我们用open系统调用后才能得到的，而文件全路经直接写就可以了。

stat和lstat的区别：当文件是一个符号链接时，lstat返回的是该符号链接本身的信息；而stat返回的是该链接指向的文件的信息。（似乎有些晕吧，这样记，lstat比stat多了一个l，因此它是有本事处理符号链接文件的，因此当遇到符号链接文件时，lstat当然不会放过。而 stat系统调用没有这个本事，它只能对符号链接文件睁一只眼闭一只眼，直接去处理链接所指文件喽）
```



当 使用 stat 查看 一个文件的属性 

```
  文件：testfile
  大小：4096      	块：8          IO 块：4096   目录
设备：803h/2051d	Inode：2632219     硬链接：2
权限：(0775/drwxrwxr-x)  Uid：( 1000/     xxx)   Gid：( 1000/     xxx)
最近访问：2022-07-06 10:37:02.701769744 +0800
最近更改：2022-07-06 10:37:02.701769744 +0800
最近改动：2022-07-06 10:37:02.701769744 +0800
创建时间：2022-07-06 10:37:02.701769744 +0800
```





### **struct stat 结构体详解**

```c
struct stat
{
    dev_t     st_dev;     /* ID of device containing file */文件使用的设备号     
    ino_t     st_ino;     /* inode number */    索引节点号 
    mode_t    st_mode;    /* protection */  文件对应的模式，文件，目录等
    nlink_t   st_nlink;   /* number of hard links */    文件的硬连接数  
    uid_t     st_uid;     /* user ID of owner */    所有者用户识别号
    gid_t     st_gid;     /* group ID of owner */   组识别号  
    dev_t     st_rdev;    /* device ID (if special file) */ 设备文件的设备号
    off_t     st_size;    /* total size, in bytes */ 以字节为单位的文件容量   
    blksize_t st_blksize; /* blocksize for file system I/O */ 包含该文件的磁盘块的大小   
    blkcnt_t  st_blocks;  /* number of 512B blocks allocated */ 该文件所占的磁盘块  
    time_t    st_atime;   /* time of last access */ 最后一次访问该文件的时间   
    time_t    st_mtime;   /* time of last modification */ /最后一次修改该文件的时间   
    time_t    st_ctime;   /* time of last status change */ 最后一次改变该文件状态的时间   
};
```

dev_t   info_t  是类型修饰符，  st_dev  ，st_ino 是变量 。

### **stat结构体中的st_mode **

则定义了下列数种情况：

```c
    S_IFMT   0170000    文件类型的位遮罩
    S_IFSOCK 0140000    套接字
    S_IFLNK 0120000     符号连接
    S_IFREG 0100000     一般文件
    S_IFBLK 0060000     区块装置
    S_IFDIR 0040000     目录
    S_IFCHR 0020000     字符装置
    S_IFIFO 0010000     先进先出
​
    S_ISUID 04000     文件的(set user-id on execution)位
    S_ISGID 02000     文件的(set group-id on execution)位
    S_ISVTX 01000     文件的sticky位
​
    S_IRUSR(S_IREAD) 00400     文件所有者具可读取权限
    S_IWUSR(S_IWRITE)00200     文件所有者具可写入权限
    S_IXUSR(S_IEXEC) 00100     文件所有者具可执行权限
​
    S_IRGRP 00040             用户组具可读取权限
    S_IWGRP 00020             用户组具可写入权限
    S_IXGRP 00010             用户组具可执行权限
​
    S_IROTH 00004             其他用户具可读取权限
    S_IWOTH 00002             其他用户具可写入权限
    S_IXOTH 00001             其他用户具可执行权限
​
    上述的文件类型在POSIX中定义了检查这些类型的宏定义：
    S_ISLNK (st_mode)    判断是否为符号连接
    S_ISREG (st_mode)    是否为一般文件
    S_ISDIR (st_mode)    是否为目录
    S_ISCHR (st_mode)    是否为字符装置文件
    S_ISBLK (s3e)        是否为先进先出
    S_ISSOCK (st_mode)   是否为socket
    若一目录具有sticky位(S_ISVTX)，则表示在此目录下的文件只能被该文件所有者、此目录所有者或root来删除或改名，在linux中，最典型的就是这个/tmp目录啦。
​
```

### st_mode 的结构

st_mode 主要包含了 3 部分信息：

- 15-12 位保存文件类型      7种情况
- 11-9 位保存执行文件时设置的信息       特殊属性
- 8-0 位保存文件访问权限       9 种情况

前16位    可以使用 掩码来过滤  出不同的文件权限



|      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |      |
| :--: | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
|  15  | 14   | 13   | 12   | 11   | 10   | 9    | 8    | 7    | 6    | 5    | 4    | 3    | 2    | 1    | 0    |



```c
// 八进制，过滤出前四位表示的文件类型
S_IFMT 0170000 bit mask for the file type bit fields

// bit12 ~ bit15
    
S_IFSOCK 0140000 socket 
S_IFLNK 0120000 symbolic link 
S_IFREG 0100000 regular file 
S_IFBLK 0060000 block device 
S_IFDIR 0040000 directory 
S_IFCHR 0020000 character device 
S_IFIFO 0010000 FIFO


// 特殊属性， 分别为set-user-ID位、set-group-ID位和sticky位
S_ISUID 0004000 set UID bit 
S_ISGID 0002000 set-group-ID bit (see below) 
S_ISVTX 0001000 sticky bit (see below)


// Permission属性区域的bit0~bit8，即st_mode字段的最低9位，代表文件的许可权限，
// 标识了文件所有者（owner）、组用户（group）、其他用户（other）的
// 读（r）、写（w）、执行（x）权限。
S_IRWXU 00700 mask for file owner permissions 
S_IRUSR 00400 owner has read permission 
S_IWUSR 00200 owner has write permission 
S_IXUSR 00100 owner has execute permission 
S_IRWXG 00070 mask for group permissions 
S_IRGRP 00040 group has read permission 
S_IWGRP 00020 group has write permission 
S_IXGRP 00010 group has execute permission 
S_IRWXO 00007 mask for permissions for others (not in group) 
S_IROTH 00004 others have read permission 
S_IWOTH 00002 others have write permission 
S_IXOTH 00001 others have execute permission
```

### 使用 stat 模拟 ls 指令

（显示文件类型信息）

```c
#include<stdio.h>
#include<string.h>
#include<sys/types.h>
#include<sys/stat.h>
#include<stdlib.h>
#include<time.h>
#include<pwd.h>
#include<grp.h>
int main(int argc ,char *argv[])
{
    //传入的参数就是文件名（）
    if(argc<2) //当不传参数的时候，默认argc是1
    {
        printf("./a.out filename\n");
exit(1);
    }
    struct stat st[argc];  //定义了结构体数组 ，可以处理多个文件或目录
   while(argc) 
   {									
    int ret=stat(argv[argc],&st[argc]); //把这个结构体数组作为参数传进去。 第一个参数是文件名，第二个参数是argc个stat类型的结构体，stat作为传入参数，包含的信息是传入文件名的信息
    if(ret ==-1) //通过返回值判断是否执行成功
    {
        perror("stat"); 
    }
    char perms[argc][11];
    switch (st[argc].st_mode & S_IFMT)//这里是与运算。  //作为传出参数，stat结构体就存放了文件信息
    {
    case S_IFLNK:
        perms[argc][0]='l';
      break;

    case S_IFDIR:
        perms[argc][0]='d';
        break;

    case S_IFREG:
        perms[argc][0]='-';
        break;

    case S_IFBLK:
        perms[argc][0]='b';
        break;

    case S_IFCHR:
        perms[argc][0]='c';
        break;


    case S_IFSOCK:
        perms[argc][0]='s';
        break;


    case S_IFIFO:
        perms[argc][0]='p';
        break;
    default:
        perms[argc][0]='?';
        break;
    }
//判断文件的访问权限
//文件所有者
perms[argc][1]=(st[argc].st_mode & S_IRUSR)? 'r' : '-' ;
perms[argc][2]=(st[argc].st_mode & S_IWUSR)? 'w' : '-' ;
perms[argc][3]=(st[argc].st_mode & S_IRUSR)? 'x' : '-' ;

perms[argc][4]=(st[argc].st_mode & S_IRGRP)? 'r' : '-' ;
perms[argc][5]=(st[argc].st_mode & S_IWGRP)? 'w' : '-' ;
perms[argc][6]=(st[argc].st_mode & S_IXGRP)? 'x' : '-' ;
perms[argc][7]=(st[argc].st_mode & S_IROTH)? 'r' : '-' ;
perms[argc][8]=(st[argc].st_mode & S_IWOTH)? 'w' : '-' ;
perms[argc][9]=(st[argc].st_mode & S_IXOTH)? 'x' : '-' ;
//硬链接数
int linkNum=st[argc].st_nlink;
//文件所有组
char* fileUser=getpwuid(st[argc].st_uid)->pw_name;
//文件所属组
char *fileGrp=getgrgid(st[argc].st_gid)->gr_name;
//文件大小
    int filesize=(int )st[argc].st_size;
    //修改时间
char*time=ctime(&st[argc].st_mtime);
char mtime[512]={0};
strncpy(mtime,time,strlen(time)-1);
char buf[1024];
sprintf(buf,"%s %d %s %s %d %s %s",perms[argc],linkNum,fileUser, fileGrp,filesize,mtime,argv[1]);
printf("%s\n",buf);

argc--;
   }

   return 0;
}

```

------

## access 函数 

头文件 和 函数结构

```
       #include <unistd.h>

       int access(const char *pathname, int mode);

       #include <fcntl.h>           /* Definition of AT_* constants */
       #include <unistd.h>

```

```c
#include <stdio。h>
#include <std1ib. h>
#include <unistd，h>
int main (int argc, char* argv [] )
{
if(argc <2)
{
printf ("a.out filename\n") ; 通过argv传入文件名
exit(1) ;
}
int ret = access (argv[1]，W OK) ;
if (ret == -1)
{
perror ("access") ;
exit(1) ;
}
printf ("you can write this file. \n") ;
return 0 
```

## chmod 函数

修改权限的函数

```c
       #include <sys/stat.h>

       int chmod(const char *pathname, mode_t mode);
       int fchmod(int fd, mode_t mode);
```

## chown 函数

```c
       #include <unistd.h>

       int chown(const char *pathname, uid_t owner, gid_t group);
       int fchown(int fd, uid_t owner, gid_t group);
       int lchown(const char *pathname, uid_t owner, gid_t group);

       #include <fcntl.h>           /* Definition of AT_* constants */
       #include <unistd.h>

```

uid 和 gid 的获取

```
etc/passwd
可以使用 man 5 passwd 查看
```

```
avahi-autoipd:x:110:119:Avahi autoip daemon,,,:/var/lib/avahi-autoipd:/usr/sbin/nologin
110 是用户id    
119 是组id
```

```c
#include <stdio . h>
#include <s tdlib . h>
int main (int argc ,char* argv [] )
{
if (argc < 2)
printf ("a. out filename! \n") ;
exit (1) ;
}
// user->ftp
//group->ftp
int
ret = chown (akgv[1], 116, 125) ;
if (ret == -1)
perror (" chown") ; 
exit (1) ;
}
return 0 ;
}

```



## truncate 函数

实现文件扩展的函数

```c
#include <unistd.h>
       #include <sys/types.h>

       int truncate(const char *path, off_t length);
       int ftruncate(int fd, off_t length);

```

```
将参数path,指定的文件大小改为参数1 ength指定的大小。如果原来的文件大小此参数
length大，则超过的部分会被删去。
```

## link 函数

```c
#include <unistd.h>
int link(const char *oldpath, const char *newpath);
```

```
 oldpath: 原始文件名
newpath: 新的硬链接名
```

测试代码：

```c
// mylink.c
#include <unistd.h>
#include <stdio.h>
 
int main(int argc, char* argv[]) 
{
  if(link(argv[1], argv[2]) == -1) {
    perror("link");
  }
  return 0;
}
```

## symlink   函数

创建软连接    参数和上面的link 相同

## readlink 函数

读取文件软连接

```c
       #include <unistd.h>

       ssize_t readlink(const char *pathname, char *buf, size_t bufsiz);

       #include <fcntl.h>           /* Definition of AT_* constants */
       #include <unistd.h>

```

之前说过，软连接就是文件的快捷方式，保存的是  源文件的名字，所以读取文件软链接就得有一个缓冲区存放文件名字

```c
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
int main(int argc ,char**argv)
{
if(argc<2)
{
printf("a.out softlink\n"); //传入一个软链接参数
exit(1);
}
char buf[512];
int ret=readlink(argv[1],buf,sizeof(buf)); 
if(ret==-1)
{
perror("readlink");
exit(1);
}
buf[ret]=0;
printf("buf=%s\n",buf);
return 0;
}

```

## unlink 函数

##  

```
       #include <unistd.h>

       int unlink(const char *pathname);

       #include <fcntl.h>           /* Definition of AT_* constants */
       #include <unistd.h>

```

```
该函数用来删除pathname 指定的目录项，并将由pathname所引用的文件的链接数减一。但是文件的内容可能不会删除，如果此时有其他指向该文件的链接，则仍可通过其他链接访问文件。或是有进程打开了该文件，那么其内容也不会被删除

所以当关闭一个文件时，内核首先检查打开该文件的进程，如果该数为0，然后检查其链接数，如果该数也是0，那么就删除该文件内容。

我们运行一个程序时有时需要创建一些临时文件。如果进程运行过程中突然终止了，而临时文件还没来的及删除，那么就会遗留下很多没用的临时文件

unlink提供了解决这个问题的一种方法。创建一个临时文件后一刻调用unlink删除文件。但是进程还是打开该文件的，所以该临时文件内容依旧是能被访问的。但是进程终止后，该文件内容就会被删除。
```



```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include<fcntl.h>
   #include <sys/types.h>
       #include <unistd.h>
 
int main ()
{
int fd = open ("tempfile",  O_CREAT | O_RDWR, 0664) ;
if(fd== -1)
{
perror ("open") ;
exit(1) ;
}
//删除临时file
int ret = unlink ("tempfile");   //testfile 是不存在的文件，如果只执行 unlink 就会删掉，所以先要创建并打开testfile文件，让其处于使用状态。
//write file
write (fd, "hello", 5) ;   //写入数据
lseek (fd,0,SEEK_SET);     //重置文件指针
//read fike
char buf[256]={0};      //读出数据
int len=read(fd,buf,sizeof(buf));
//将读出的内容，写道屏幕上
write(1,buf,len);      //验证这个文件确实存在
//close file
close(fd);
return 0;
}

```

执行完这个程序后， 可以执行  ls  ,查看 该目录下 是否有 testfile 文件，

如果没有，证明，该文件仅在打开时 存在 ，关闭后销毁





# Linux目录操作函数



文件或目录重命令函数

## rename 

```c
       #include <stdio.h>

       int rename(const char *oldpath, const char *newpath);

       #include <fcntl.h>           /* Definition of AT_* constants */
       #include <stdio.h>

```



```c
#include <stdio.h>
int main(int argc,char **argv)
{
if(argc < 3)
{
printf("Usage: %s old_name new_name\n",argv[0]);
return -1;
}
printf("%s => %s\n", argv[1], argv[2]);
if(rename(argv[1], argv[2]) < 0 )
printf("error!\n");
else
printf("ok!\n");
return 0;
}

```

```
./a.out 旧的文件（目录）名，新的文件（目录）名  
```

注意：

```
(1) 如果oldname为一个文件而不是目录，那么为该文件更名。在这种情况下，如果newname作为一个目录已存在，则它不能重命名一个目录。如果newname已存在，而且不是一个目录，则先将其删除 （rename会负责删除操作） 然后将oldname更名为newname。对oldname所在目录以及newname所在的目录，调用进程必须具有写许可权，因为将更改这两个目录。
(2) 如若oldname为一个目录，那么为该目录更名。如果newname已存在，则它必须是一个目录，而且该目录应当是空目录（空目录指的是该目录中只有. 和.. 项）。如果newname存在（而且是一个空目录），则先将其删除，然后将oldname更名为newname。另外，当为一个目录更名时，newname不能包含oldname作为其路径前缀。例如，不能将/usr更名为/usr/foo/testdir，因为老名字（ /usr/foo）是新名字的路径前缀，因而不能将其删除。
(3) 作为一个特例，如果oldname和newname引用同一文件，则函数不做任何更改而成功返回。
```



## chdir 函数

修改当前进程的路径

```c
     #include <unistd.h>
       int chdir(const char *path);
       int fchdir(int fd);
```



## getcwd 函数

```c
  #include <unistd.h>

       char *getcwd(char *buf, size_t size);

```

获取当前进程的目录 相当于 （pwd）

通常是 上面两个函数搭配使用(示例)

```c
#include<stdio.h>
#include<stdlib.h>
#include <unistd.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

int main(int argc,char**argv)
{

if(argc<2)
{
printf("未传参");
exit(1);
}  //将传入的路径保存在参数argv[1]中

char buf[128];
getcwd(buf,sizeof(buf));
printf("current1  dir :%s\n",buf);  //使用getcwd 当前的文件路径
if(chdir(argv[1])>-1)   //使用chdir 改变这个程序进程的路径
{
getcwd(buf,sizeof(buf)); //获得改变后的路径
printf("current2 dir :%s\n",buf);    
}
else{
perror("chdir:: ");
exit(1);
}
    int fd= open("test",O_CREAT|O_RDWR,0777);  //创建一个文件，是在修改之后的路径上 创建的。
if(fd==-1)
{

perror("open::");
exit(1);

}
close(fd);
return 0;
}

```

**在程序内改变路径之后，创建的文件所在的路径是在 改变之后的路径上。**

```
./a.out ../../
```

此时创建的文件在 上两级目录。

**注意：**

getcwd 会将工作目录的绝对路径复制到buf所指向的空间中,但如果路径长度大于size,则会返回NULL,错误代码为ERANGE.所以我们在定义path的时候得定义的足够大,但这样又会使得[内存](https://so.csdn.net/so/search?q=内存&spm=1001.2101.3001.7020)浪费,Linux的文件名长度限制为255个英文字符,理论上绝对路径的大小应该可以足够大,故而这样使用总有返回NULL的时候,所以getcwd()个我们提供了下面的一种用法:

可以采取令 buf 为 NULL并使 size 为零(百度百科里介绍可以使用负值但我测试的时候是段错误)来使 getcwd 调用 malloc 动态给 buf 分配,但是这种情况要特别注意使用后释放缓冲以防止内存泄漏。

```c

int main(void)

{

char *path = NULL;

path = getcwd(NULL,0);

puts(path);

free(path);

return 0;

}
```

在fchdir 中也可以使用 fd 来 设定  新的路径

函数说明：fchdir()用来将当前的工作目录改变成以参数fd 所指的文件描述词。

```c
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
main()
{
  int fd;
  fd = open("/tmp", O_RDONLY);
  fchdir(fd);
  printf("current working directory : %s \n", getcwd(NULL, NULL));
  close(fd);
}

```

执行 

```c
current working directory : /tmp
```

## opendir

打开文件函数

```c
       #include <sys/types.h>
       #include <dirent.h>

       DIR *opendir(const char *name);

```

## readdir

读取文件函数

```c
   #include <dirent.h>

       struct dirent *readdir(DIR *dirp);
```

可见 readdir函数的返回值是一个结构体  

结构体的内容是

```
函数说明：readdir()返回参数dir 目录流的下个目录进入点。结构dirent 定义如下：
struct dirent
{
    ino_t d_ino; //d_ino 此目录进入点的inode
    ff_t d_off; //d_off 目录文件开头至此目录进入点的位移 
    signed short int d_reclen; //d_reclen _name 的长度, 不包含NULL 字符   。  是文件名字的长度
    unsigned char d_type; //d_type d_name 所指的文件类型    
    har d_name[256];  存放文件名字
};
```

文件类型（d_type）有

```
	DT_REG	A regular file. 常规文件
    DT_DIR A directory. 目录
    DT_FIFO   A named pipe, or FIFO. See FIFO Special Files. 一个命名管道，或FIFO。
    DT_SOCK  A local-domain socket.套接字
    DT_CHR  A character device.  字符设备
    DT_BLK  A block device.   块设备
    DT_LNK  A symbolic link. 符号链接
```



```c
 #include <sys/types.h>
#include<string.h>
#include<stdio.h>
#include<stdlib.h>

       #include <dirent.h>
int getFileNum(char *root)
{
//open dir
DIR*dir =NULL;
dir =opendir(root);//root 是传入的文件根目录
    //DIR 类似于文件指针，指向可能的第一个 文件或者目录
if(dir==NULL)
{
perror("opendir");
exit(1);
}
struct dirent*ptr=NULL;
char path[1024]={0};
int total=0;
while(NULL!=(ptr=readdir(dir)))
{
//过滤 。 和  。。 
if(strcmp(ptr->d_name, ".")==0||strcmp(ptr->d_name,"..")==0)
{
continue;
}
if(ptr->d_type==DT_DIR)//如果是目录
{
sprintf(path,"%s/%s",root,ptr->d_name);
//将目录路径格式化保留到数组中
total+=getFileNum(path);//递归读目录
}
if(ptr->d_type=DT_REG)//如果是普通文件
{
total++;
}
}

closedir(dir);
return total;
}
int main(int argc,char**argv)
{

if(argc<2)
{
printf("./a.out dir\n");
exit(1);
}
int total=getFileNum(argv[1]);
printf("%s has file numbers%d\n",argv[1],total);
return 0;
}
```

```
./a.out  ~ 
```

查看家目录的文件个数

## dup 

功能：复制文件描述符，重定向输入输出。

```
#include <unistd.h>
int dup(int oldfd);

	成功：dup函数返回当前系统可用的最小整数值。
```

```
复制文件描述符后，新旧文件描述符的特点：

使用dup或dup2复制文件描述符后，新文件描述符和旧文件描述符指向同一个文件，管道或网络连接，共享文件的锁定、读写位置和各项权限。
当关闭新的文件描述符时，通过旧文件描述符仍可操作文件。
当关闭旧的文件描述符时，通过新的文件描述符仍可操作文件。


```



```c
#include<stdio.h>
#include<stdlib.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

int main()
{

int fd=open("a.txt",O_RDWR);//文件描述符默认是3, 0 是标准输入，1 是标准输出 ，2是标准错误
if(fd==-1)
{
perror("open");
exit(1);

}
printf("file open fd=%d\n",fd);

int ret=dup(fd);//会找到未被分配的最小文件描述符给这个复制的 ret
if(ret==-1)//此时  两个文件描述符都指向同一个文件
{


perror("dup");
exit(1);

}
printf("dup fd =%d\n",ret);
char *buf="aaaaaaaaaa\n";
char *buf1="bbbbbbbbbbbb\n";

write(fd,buf,strlen(buf));//两次写入是按照不同的文件描述符来的

write(ret,buf1,strlen(buf1));//如果写入文件内容是这两句话 ，则证明是指向同一个文件
close(fd);

return 0; 

}

```



是共享文件偏移量和文件状态标志。
比如：其中一个使用lseek修改文件偏移量，另一个也会改变（读写等操作位置会改变）。

```c
/* 例子：复制文件描述符，并向文件写数据 */
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <unistd.h>
#include <fcntl.h>

void main()
{
	int oldfd,newfd;
	char *oldstr = "Write by oldfd.\n";
	char *newstr = "Write by newfd.\n";

	oldfd = open("test.txt", O_RDWR|O_CREAT, 0644);
	printf("oldfd = %d\n", oldfd);

	if(oldfd == -1) {
		printf("File open error\n");
		exit(-1); 
	}

	/* 开始复制 */
	newfd = dup(oldfd);
	printf("newfd = %d\n", newfd);

	/* 使用oldfd写 */
	write(oldfd, oldstr, strlen(oldstr));
	if(close(oldfd) == -1) {
		printf("Close oldfd error.\n");
		exit(-1);
	}

	/* 使用newfd写 */
	write(newfd, newstr, strlen(newstr));
	if(close(newfd) == -1) {
		printf("Close newfd error.\n");
		exit(-1);
	}
	exit(0);
}


```

```c
终端输出：
oldfd = 3
newfd = 4

查看test.txt：
Write by oldfd.
Write by newfd.

```



## dup2

```
int dup2(int oldfd, int newfd);

返回值：

		 dup2函数返回第一个不小于newfd的整数值，分两种情况：
		 	1. 如果newfd已经打开（其他文件在使用这个文件描述符），则先将其关闭，再复制文件描述符到你要使用的这个文件上；
		 	2. 如果newfd等于oldfd，则dup2函数返回newfd，而不关闭它。
	失败：dup和dup2函数均返回-1，并设置errno。
```



```c
#include<stdio.h>
#include<stdlib.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

int main()
{
int fd=open("english.txt",O_RDWR);//文件描述符默认是3, 0 是标准输入，1 是标准输出 ，2是标准错误
    //以读写权限打开，如果不使用 fseek 移动文件指针，文件指针是在文件最开始的地方的，写入数据，就会覆盖掉旧的数据。 可以使用  fseek(fd,0,SEEK_END)
if(fd==-1)
{
perror("open");
exit(1);

}
int fd1=open("a.txt",O_RDWR);//文件描述符默认是3, 0 是标准输入，1 是标准输出 ，2是标准错误
if(fd1==-1)
{
perror("open");
exit(1);

}
printf("最开始的文件描述符");
printf("file open fd=%d\n",fd);

printf("file open fd1=%d\n",fd1);

int ret=dup2(fd1,fd);//fd1 覆盖 掉fd   ,fd 指向的文件将会关闭，fd 将会和fd1 指向同一个文件（a.txt）
if(ret==-1)//此时  两个文件描述符都指向同一个文件
{


perror("dup");
exit(1);

}
printf("使用dup2 之后"); 
printf("file open fd=%d\n",fd);
printf("file open fd1=%d\n",fd1);
printf("dup2 函数的 ret =%d\n",ret);

char *buf="aaaaaaaaaa\n";

write(fd,buf,strlen(buf));//两次写入是按照不同的文件描述符来的

write(fd1,"hello,world",11);//如果写入文件(a.txt)内容是这两句话 ，则证明是指向同一个文件(a.txt)
close(fd);
close(fd1);
return 0; 

}

```

## dup2 重定向输入输出

（dup 2 ()   是在输入输出之前进行的）

### 重定向输入

标准输入所对应的设备是键盘，也就是说，标准输入相应的文件描述符，当它进行read时，实际上是read键盘输入的数据，而如果不想让键盘作为标准输入呢？比如说让程序从某个文件中读取输入数据，这就需要重定向标准输入了。

        举个例子，我用一个文件描述符3对应一个打开的文件A，然后调用dup2(3,0)函数，这样就使得标准输入文件描述符0重定向到了文件描述符3所对应的文件表项上，原本应该从键盘获取数据，现在变成从文件A获取数据，此时如果调用read或者其他标准输入函数如cin、getline、getchar等函数，都是从文件A中读取数据。如下所示：
```cpp
#include <string.h>
#include <unistd.h>
#include <iostream>
#include <fcntl.h>
 
using namespace std;
 
int main()
{
	string rdstr;
	int fd = -1;
	if((fd = open("test.txt",O_RDWR)) == -1)
	{
		cout<<"open failed !"<<endl;
		return -1;
	}
	dup2(fd,0);    //重定向标准输入到外部文件test.txt中
 
	while(getline(cin,rdstr))     //用getline从标准输入中获取数据
	{
		cout<<rdstr<<endl;    //通过标准输出将读入的数据打印出来
	}
 
        close(fd);
	return 0;
}
```

 可以看到，这里虽然调用了getline函数，它会从标准输入中获取数据，在一般情况下会阻塞等待键盘输入，但是由于这里标准输入重定向到了外部文件test.txt中，因此getline就直接从test.txt中获取每一行数据，与键盘输入无关。

### 重定向输出

​    标准输出的设备是显示器，通过标准输出文件描述符1进行write时，数据会直接输出到显示器上。那么如果不想让标准输出输出到显示器上呢？比如说想让cout、printf直接将数据输出到文件中，那么就需要重定向标准输出了。如下所示：

```cpp
#include <string.h>
#include <unistd.h>
#include <iostream>
#include <fcntl.h>
#include <errno.h>
 
using namespace std;
 
int main()
{
	int fd = -1;
	if((fd = open("test.txt",O_RDWR|O_CREAT|O_TRUNC)) == -1)  //先将test.txt的文本内容清空
	{
		cout<<"open failed !"<<endl;
		return -1;
	}
	dup2(fd,1);   //重定向标准输出到外部文件test.txt
 
	cout<<"重定向标准输出测试！"<<endl;    //向标准输出输出数据
        close(fd);
	return 0;
}
```

### 重定向恢复

​    在进行重定向后，如果想要恢复到重定向之前的状态，可以在重定向之前用dup函数保留该文件描述符对应的文件表项，然后在需要恢复重定向的时候使用dup2重定向到原来的文件表项，以重定向后恢复标准输出为例，如下所示：

```cpp
#include <unistd.h>
#include <iostream>
#include <fcntl.h>
#include <stdio.h>
using namespace std;
 
int main()
{
	int fd = -1;
	if((fd = open("test.txt",O_RDWR|O_CREAT|O_TRUNC)) == -1) //打开并清空外部文件
	{
		cout<<"open failed !"<<endl;
		return -1;
	}
 
	int oldfd = dup(1);     //保存标准输出对应的文件表项
 
	dup2(fd,1);    //重定向标准输出到外部文件test.txt中
 
	cout<<"重定向标准输出测试！"<<endl;    //重定向测试
  
	dup2(oldfd,1);   //将重定向后的文件描述符1再次重定向到一开始保存的标准输出对应的文件表项中
 
	cout<<"重定向标准输出恢复测试！"<<endl;   //重定向恢复测试
 
	close(fd);
	close(oldfd);
	return 0;
}
```

​    根据运行结果可知，在第一次重定向后，cout输出信息是输出到了外部文件中，当再次重定向进行恢复之后，此时的cout就将数据输出到显示器上了，回到了最原始的标准输出。



  以上介绍了dup和dup2函数，并且使用dup2函数实现了标准输入、标准输出和标准错误输出的重定向，以及dup和dup2函数共同使用实现重定向恢复。需要注意的是，在调用dup或者dup2函数之后，至少会有两个文件描述符指向同一个文件表项，由于文件表项中含有文件标志（即open时的flag）以及文件偏移等信息，因此这些信息对于这些文件描述符来说都是共享的。



## fcnt 1 函数

```c
    #include <unistd.h>
       #include <fcntl.h>
       int fcntl(int fd, int cmd, ... /* arg */ );
```

可见这是可变参数的函数

```
#include<unistd.h>  
#include<fcntl.h>  
int fcntl(int fd, int cmd);  
int fcntl(int fd, int cmd, long arg);  
int fcntl(int fd, int cmd ,struct flock* lock);  
```

作用：

```
F_ DUPFD
复制文件描述符
F_ GETFD
获取文件描述符标志
F_ SETFD
设置文件描述符标志
F_ GETFL
获取文件状态标志
F_ SETFL 
设置文件状态标志dhu@:6006102
F_ GETLK
获取文件锁
F_ SETLK
设置文件锁
F_ SETLKW
类似F_ SETLK， 但等待返回
F GETOWN
获取当前接收SIGIO和SIGURG信号的进程ID和进程组ID
F_ SETOWN
设置当前接收SIGI0和SIGURG信号的进程和ID进程组ID

```



```
（1）F_DUPFD

与dup函数功能一样，复制由fd指向的文件描述符，调用成功后返回新的文件描述符，与旧的文件描述符共同指向同一个文件。

（2）F_GETFD

读取文件描述符close-on-exec标志

（3）F_SETFD

将文件描述符close-on-exec标志设置为第三个参数arg的最后一位

（4）F_GETFL

获取文件打开方式的标志，标志值含义与open调用一致

（5）F_SETF

设置文件打开方式为arg指定方式
```

其中 F_GETFL 包含：

```
只读打开日0_ RDONLY
只写打开日
O_ _WRONLY
读写打开日0_ _RDWR
F_ GETFL o
执行打开日.
0_ EXEC
搜索打开目录日0_ SEARCH
e
追加写日
O_ APPEND
非阻塞模式日0_ NONBLOCK
F_ SETFL日

```

F_SETFL  只能修改下面：

```
可更改的几个标识
O_ APPEND
O_ NONBLOCK
```

### 例子

```c
#include<stdio.h>
#include <unistd.h>
#include <fcntl.h>
#include<stdlib.h>
#include<sys/types.h>
#include<sys/stat.h>
#include<string.h>
int main()
{
/*
//复制文件描述符的方法
fcntl(fd,F_GETFD);
int fd1=F_GETFD ;
*/
int fd;
int flag;
//测试字符串
char *p ="我们是一个有中国特色的社会主义国家！！！！";
char *q="呵呵，社会主义好哇。。。。。";
//以只写的方式打开文件
fd=open("test.txt",O_WRONLY);
if(fd==-1)
{
perror("open");
exit(1);
}
//输入新的内容，该部分会覆盖原来旧的内容
if(write(fd,p,strlen(p))==-1)
{
perror("write");
exit(1);
}
//使用F_GETTFL 命令得到文件描述符
flag=fcntl(fd,F_GETFL,0);
if(flag==-1)
{
perror("fcntl");
exit(1);
}
//将文件描述符 添加   “追加写”选项
flag |=O_APPEND;//两个int 类型的值 使用按位或   ////////////////////////////////  重要重要  /////////////////////////
//将文件状态修改为追加写
if(fcntl(fd,F_SETFL,flag)==1)
{
perror("fcntl -- append write");
exit(1);
}
//再次输入新内容，该内容会追加到旧内容的后面
if(write(fd,q,strlen(q))==-1)
{

perror("write again");
exit(1);

}
close(fd); 
return 0;
}

```

 
