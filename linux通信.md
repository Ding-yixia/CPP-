进程间通信

# 无名管道 pipe的通信

## 创建 父子进程 间 的管道通信 

管道是单工的，数据只能从一个放向流动，需要双向通信时需要建立两个管道。

数据的读入和写入：一个进程向管道中写的内容被管道的另一端的进程读取。写入的内容每次都添加在管道缓冲区的末尾，并且每次都是从缓冲区的头部读出数据。

匿名管道
在关系进程中进行（父进程和子进程、兄弟进程之间），由pipe系统调用，管道由父进程建立，管道位于内核空间，实际是一块缓存。

```
#include <unsitd.h>
int pipe(int fd[2]);
```

返回：成功返回0，出错返回-1。

两个文件描述符数组：
fd[0]:为pipe的读端，用于读取管道；  对应标准读出    0
fd[1]:为pipe的写端，用于写入管道； 对应标准输出     1

### 阻塞状态的管道通信

```c
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<string.h>
int main(void)
{
int fd[2];
pid_t pid;

int ret=pipe(fd);
if(ret==-1)
{
perror("pipe error:");
exit(1);
}
pid=fork();
if(pid==-1)

{
perror("fork error");
exit(1);
}
else if(pid==0)       //子进程读入数据
{
close(fd[1]);     //关闭 管道写
char buf[1024];
ret=read(fd[0],buf,sizeof(buf));  //fd[0] 对应管道读
if(ret==0) //ret 为 0 则读完
{
printf("read  end\n");
}
write(0,buf,ret);     // 0 对应标准输出（读数据） 
close(fd[0])
    //结束后关闭 管道读
}
else{              //父进程 写出数据

close(fd[0]); //关闭 管道 读
char* buf2="hello pipe\n"; 
write(fd[1],buf2,strlen(buf2));   //fd[1] 对应管道写    // 如果注释掉此行，即 不向管道写入数据，就会让子进程阻塞等待
close(fd[1]); // 结束后关闭 管道写

}
return 0;
}

```

注明： 这里并不需要 让  读进程 sleep() 几秒，以便写进程 写。

因为 ，read() 函数读取设备 会阻塞  直到buf 中 有数据。

当读一个写段已经被关闭的管道时，在所有的数据都被读取后，**read返回0**(read返回0表示已经读到文件结束符)；



**当管道中无数据写入的时候，read 函数将阻塞等待数据（前提是至少要有一个管道的写端口 不是关闭的 ，否则 read 函数将直接返回 0  ，因为不可能有数据写入，不必再等待数据）**

## 		演示  read 函数阻塞等待数据的情况

```c
#include<stdio.h>
#include<unistd.h>
#include<stdlib.h>
int main()
{
int fd[2];
pipe(fd);
pid_t pid =fork();

if(pid==0)
{
close (fd[0]);// 关闭管道读
printf("I am  child ====parent pid :%d,child  pid : %d======\n",getppid(),getpid());
char buff[6]="hello";
//write
close(fd[1]);   //提前将 管道写   关闭了  ，管道无数据 就会阻塞
write(fd[1],buff,sizeof(buff));   //先写入数据，再关闭管道写。 如果提前关闭管道。管道中无数据，父进程的 read 函数就会阻塞等待。

//close(fd[1]);    // 子进程的 管道 写  在  写入数据后 才关闭（让管道中有数据）
printf("=============write end=============\n");

}
if(pid>0)
{
char buf[1024];
//close(fd[1]);
printf("I am parent =====parent pid :%d\n",getpid());
while(1)   //设置循环 ，重复读管道数据
{
int ret=read(fd[0],buf,sizeof(buf));
if(ret==0)
{
printf("ret:%d\n",ret);
printf("=============read end=============\n");
exit(1);
}
else if(ret>0){
//write(1,buf,sizeof(buf));
printf("ret:%d\n",ret);


}
}

}
return 0; 
}

```

执行情况： （可以看到 read 进程并未结束） 

```
xxx@xxx-virtual-machine:~/JC2$ ./test06
I am parent =====parent pid :6840
I am  child ====parent pid :6840,child  pid : 6841======
=============write end=============


```

```
xxx         6841  0.0  0.0      0     0 pts/2    Z+   18:45   0:00 [test06] <defunct>
```

可以看到        子进程阻塞了

#### 演示不发生阻塞的情况

现在   我们将 子进程的 管道 写  在  写入数据后 才关闭（让管道中有数据） .   

执行情况如下

```
I am parent =====parent pid :6941
I am  child ====parent pid :6941,child  pid : 6942======
=============write end=============
ret:6    
ret:0
=============read end=============
```

并未发生阻塞

设置while 重复读，  读完之后若返回 0  ，则说明 读完 管道所有数据。

#### 演示 将  所有进程的   管道   写端口关闭

read 函数意识到   不可能再有数据写进管道了，如果管道没数据就直接返回0，不进行阻塞。如果管道残留有数据 ，就会读出后，返回0.

```c
#include<stdio.h>
#include<unistd.h>
#include<stdlib.h>
int main()
{
int fd[2];
pipe(fd);
pid_t pid =fork();

if(pid==0)
{

printf("I am  child ====parent pid :%d,child  pid : %d======\n",getppid(),getpid());
char buff[6]="hello";
//write
    close(fd[1]);// 关闭管道写 ,演示 管道无数据
write(fd[1],buff,sizeof(buff));   //先写入数据，再关闭管道写。 
    //如果提前关闭管道。管道中无数据，父进程的 read 函数就会阻塞等待。前提是父进程中不要关闭管道写   （两个都关闭管道写 read 函数 就直接返回0 ，因为此时 不可能再有数据输入了。）
// close(fd[0]);      // 演示管道 残留数据   后关闭

printf("=============write end=============\n");

}
if(pid>0)   //父进程   读数据
{
char buf[1024];
close(fd[1]); //关闭管道写
printf("I am parent =====parent pid :%d\n",getpid());
while(1)
{
int ret=read(fd[0],buf,sizeof(buf));   //循环读数据  ，不要关闭 管道 读
if(ret==0)
{
printf("ret:%d\n",ret);
printf("=============read end=============\n");
exit(1);
}
else if(ret>0){
//write(1,buf,sizeof(buf));
printf("ret:%d\n",ret);


}
}

}
return 0; 
}

```

若管道无数据，父子进程关闭 管道写

```
I am parent =====parent pid :6958
I am  child ====parent pid :6958,child  pid : 6959======
=============write end=============
ret:0
=============read end=============
```

可见的确是   直接 返回0

若管道中有数据 ，父子进程关闭管道写

```
I am parent =====parent pid :6971
I am  child ====parent pid :6971,child  pid : 6972======
=============write end=============
ret:6
ret:0
=============read end=============
```

可见在读完数据后，read 函数并不会阻塞（阻塞等待管道中有新数据 ，除非打开其中一个进程的管道写） ，而是返回 0   结束

比如我打开 父进程的管道写，  read 函数 就 会阻塞等待管道中有新数据（如下结果，就是read 未停止）

```
I am parent =====parent pid :6984
I am  child ====parent pid :6984,child  pid : 6985======
=============write end=============
ret:6

```

## 演示 write 函数阻塞等待 的情况

管道中数据满了 ，导致 write 函数 阻塞等待 管道数据被读出.

#### 如果是一个进程的话，write 会被阻塞掉

```c
#include <stdio.h>    
#include <unistd.h>    
     
int main() {    
    int pipefd[2];    
    if(pipe(pipefd) < 0) {    
        perror("pipe error");    
        return -1;    
    }    
     
    int count = 0;    
    while(1) {    
        write(pipefd[1], "0", 1);    
        ++count;    
        printf("count：%d\n", count);    
    }    
     
    return 0;    
}
```

运行结果：

```c
count：65531
count：65532
count：65533
count：65534
count：65535
count：65536
              // 管道 只能写入65536  程序将被阻塞在这
```

#### 如果是 父子进程（阻塞write）

写满管道 还想写入数据       将直接结束程序：

```c
#include<stdio.h>
#include<signal.h>
#include<fcntl.h>
#include<stdlib.h>
#include<unistd.h>
#include<string.h>
void sig_handler(int sig)
{
    printf("Catch a signal,it is NO.%d signal!\n",sig);
};

int main(void)
{
signal(SIGPIPE,sig_handler);  //捕获错误信号 
int fd[2];
pid_t pid;

int ret=pipe(fd);
if(ret==-1)
{
perror("pipe error:");
exit(1);
}
pid=fork();
if(pid==-1)

{
perror("fork error");
exit(1);
}
else if(pid==0)       //子进程读入数据
{
printf("I am child: pid :%d, ppid: %d\n",getpid(),getppid());
}
else{              //父进程 写入数据

close(fd[0]); //关闭 管道 读
     
printf("I am parent pid : %d \n",getpid());
int count = 0;    
while(1)
{
char buff[9046]="hello guys acds dscs sdvsd sgsdfvs secasdgsdcvfsd evdsv";
    while(1) {    
      int retw=  write(fd[1], buff, sizeof(buff));    
count++;
    
if(retw==0)
{
printf("========= write   end ==========\n");
}
if(retw==-1)
{

perror("write error: ");
printf("\n管道满");
exit(1); 
//break;

}
if(retw>0)
{

printf("retw: %d\n",retw);

}
}
}
printf("count:%d:",count);
close(fd[1]);

}
return 0;
}

```

让父进程一直写入数据   ， 而子进程不从管道读数据。当无法写入数据时候  ，发出13号信号，会终止程序运行。 并不会阻塞。

运行结果：

```
I am parent pid : 9372 
retw: 9046
retw: 9046
retw: 9046
retw: 9046
retw: 9046
retw: 9046
I am child: pid :9373, ppid: 9372
Catch a signal,it is NO.13 signal!
retw: 4096
Catch a signal,it is NO.13 signal!
write error: : Broken pipe  
管道满
```

###  





### **演示将所有 进程的读端口 关闭**

```c
#include<stdio.h>
#include<signal.h>
#include<unistd.h>
#include<stdlib.h>
void sig_handler(int sig)
{
    printf("Catch a signal,it is NO.%d signal!\n",sig);
};

int main()
{
int fd[2];
pipe(fd);
pid_t pid =fork();

signal(SIGPIPE,sig_handler); 
if(pid==0)
{
close(fd[0]);
printf("I am  child ====parent pid :%d,child  pid : %d======\n",getppid(),getpid());
char buff[100]="hello world I am A boy nice to meet you abcdefg";
//write
int ret=write(fd[1],buff,sizeof(buff));   //先写入数据，再关闭管道写。 
//close(fd[0]);
if(ret==-1)
{
perror("write error");
exit(1);
}
    printf("=============write end=============\n");

}
if(pid>0)   //父进程   读数据
{
char buf[6];
close(fd[1]); //关闭管道写
printf("I am parent =====parent pid :%d\n",getpid());
close(fd[0]);//关闭管道 读
while(1)
{
int ret=read(fd[0],buf,sizeof(buf));   //循环读数据  ，不要关闭 管道 读
if(ret==0)
{
printf("ret:%d\n",ret);
printf("=============read end=============\n");
exit(1);
}
else if(ret>0){
write(1,buf,sizeof(buf));
printf("ret:%d\n",ret);
}
}
}

return 0; 
}

```

执行结果： 

```
I am parent =====parent pid :7187
I am  child ====parent pid :7187,child  pid : 7188======
Catch a signal,it is NO.13 signal!
write error: Broken pipe
```

如果所有管道读端对应的文件描述符被关闭，则 write 操作会产生 SIGPIPE 信号，进而可能导致 write 进程退出。

​    在上述代码中，父子进程的读端都被关闭，此时让父进程向管道中写入数据，则会产生 13 号信号，通过 kill -l 命令可以查看这个信号就是 SIGPIPE 信号，进而导致父进程 write 出错返回 -1，并置 errno 为 EPIPE，对应的出错信息是Broken pipe。



## 设置非阻塞状态的管道通信



之前提到过 fcntl 能在运行的时候 改变 文件访问权限，现在也可以改变管道通信阻塞状态

​    LINUX中，管道的读写两端是阻塞的，例如读端会一直阻塞直到写端写入内容，才会立即返回。其实也可以将管道中的读或写端设置为非阻塞状态。

​    **如果要设置读端或者写端为非阻塞，参考以下三个步骤：**

```c
    int flags = fcntl(fd[0], F_GETFL, 0);
     flag |= O_NONBLOCK;
     fcntl(fd[0], F_SETFL, flags);
```

 

### 以读端设置为非阻塞为例： 

- **写端没有关闭，管道中没有数据可读，则read返回-1**
- **写端没有关闭，管道中有数据可读，则read返回实际读到的字节数**
- **写端已经关闭，管道中有数据可读，则read返回实际读到的字节数**
- **写端已经关闭，管道中没有数据可读，则read返回0**

以第一个程序为例，

父进程 打开管道写，但不写入数据。 子进程 打开管道读 ，但没有数据    

```c
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<string.h>
int main(void)
{
int fd[2];
pid_t pid;

int ret=pipe(fd);
if(ret==-1)
{
perror("pipe error:");
exit(1);
}
pid=fork();
if(pid==-1)

{
perror("fork error");
exit(1);
}
else if(pid==0)       //子进程读入数据
{
char buf[1024]={0};
close(fd[1]);     //关闭 管道写
ret=read(fd[0],buf,sizeof(buf));  //fd[0] 对应管道读
if(ret==0) //ret 为 0 则读完
{
printf("read  end\n");
}
if(ret==-1)
{

perror("read error");
exit(1);
}
write(0,buf,ret);     // 0 对应标准输出（读数据） 
close(fd[0]);
    //结束后关闭 管道读
}
else{              //父进程 写入数据

close(fd[0]); //关闭 管道 读

}
return 0;
}
```

结果：

```
xxx@xxx-virtual-machine:~/JC2$ ./test05
read  end
```

说明 是read 返回 了 0

将 子进程的管道写打开  ，执行后

```
xxx@xxx-virtual-machine:~/JC2$ ./test05
read error: Resource temporarily unavailable
```

说明 是read 返回 -1



### 演示 父子进程的非阻塞 write  

```c
#include<stdio.h>
#include<errno.h>
#include<signal.h>
#include<fcntl.h>
#include<stdlib.h>
#include<unistd.h>
#include<string.h>
void sig_handler(int sig)
{
    printf("Catch a signal,it is NO.%d signal!\n",sig);
};

int main(void)
{
signal(SIGPIPE,sig_handler);  
int fd[2];
pid_t pid;

int ret=pipe(fd);
if(ret==-1)
{
perror("pipe error:");
exit(1);
}
pid=fork();
if(pid==-1)

{
perror("fork error");
exit(1);
}
else if(pid==0)       //子进程读入数据
{
printf("I am child: pid :%d, ppid: %d\n",getpid(),getppid());
close(fd[1]);
/*
 int flags = fcntl(fd[0], F_GETFL, 0);
     flags |= O_NONBLOCK;
     fcntl(fd[0], F_SETFL, flags);

char buf[20]={0};
close(fd[1]);     //关闭 管道写
//while(1)
//{
*/
/*

ret=read(fd[0],buf,sizeof(buf));  //fd[0] 对应管道读
if(ret==0) //ret 为 0 则读完
{
printf("====================read  end=============\n");
exit(1);
}
if(ret==-1)
{

perror("read error");
exit(1);
}
if(ret>0)
{

printf("ret: %d\n",ret);
write(0,buf,ret);     // 0 对应标准输出（读数据） 
printf("\n");


}
*/
//}
//close(fd[0]);
    //结束后关闭 管道读
//while(1);
}
else{              //父进程 写入数据

close(fd[0]); //关闭 管道 读

 int flags = fcntl(fd[1], F_GETFL, 0);
     flags |= O_NONBLOCK;
     fcntl(fd[1], F_SETFL, flags);

printf("I am parent pid : %d \n",getpid());
int count = 0;    
while(1)
{
char buff[9046]="hello guys acds dscs sdvsd sgsdfvs secasdgsdcvfsd evdsv";
    while(1) {    
      int retw=  write(fd[1], buff, sizeof(buff));    
count++;
        
if(retw==0)
{
printf("========= write   end ==========\n");
}
if(retw==-1)
{

printf("管道满%d",errno);
perror("write error: ");
exit(1); 
//break;

}
if(retw>0)
{

printf("retw: %d\n",retw);

}
}
}
printf("count:%d:",count);
close(fd[1]);

}
return 0;
}

```

运行结果

```c
xxx@xxx-virtual-machine:~$ ./test02
I am parent pid : 10035 
retw: 9046
retw: 9046
I am child: pid :10036, ppid: 10035
retw: 9046
retw: 9046
retw: 9046
retw: 9046
retw: 4096
write error: : Resource temporarily unavailable
管道满11
xxx@xxx-virtual-machine:~$ 

```

​    从执行结果来看，设置为非阻塞后，当管道写满时，程序再次调用 write 并没有陷入等待，而是直接返回了 `-1`，并且 `errno` 的值被置为`11`，这个值的具体定义我们在前面已经查看过，其定义为`EAGAIN`。

## 模拟 PS   grep 命令

```c
#include<sys/types.h>
#include<sys/wait.h>
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>

int main()
{
int fd[2];
pipe(fd);
pid_t pid =fork();
int status;
pid_t pp;
if(pid==-1)
{

perror("perro");
exit(1);
}

else if(pid==0)
{
printf("child  : pid :%d,  ppid :%d\n",getpid(),getppid());

//int oldfd=dup(1);//保留 标准输入
close(fd[0]);//关闭管道读 
dup2(fd[1],1);//标准写重定向到管道写端
execlp("ps","ps","aux",NULL);
}
else if(pid>0)
{

printf("parent  : pid :%d,  ppid :%d\n",getpid(),getppid());
close(fd[1]); //关闭管道写
dup2(fd[0],0); //标准读重定向重定向到管道读端口,此时 管道的内容已经读到终端了，可以关闭 管道读
close(fd[0]); //关闭管道读

//回收子进程
int ret=waitpid(pid,&status,WNOHANG);
if(ret==-1)
{
perror("wait error");
}
	if(WIFEXITED(status))
	{
	printf("\nchild exit with %d\n",WEXITSTATUS(status));   // 获取 正常退出的返回值
	}
execlp("grep","grep","bash",NULL);//exec  函数后买你的代码 语句将不会执行k


}

return 0;
}

```

运行结果：

```c
parent  : pid :4120,  ppid :3980

child exit with 0
child  : pid :4121,  ppid :4120
xxx         2671  0.0  0.2  20172  5552 pts/2    Ss+  18:44   0:00 -bash
xxx         3164  0.0  0.2  20320  5728 pts/0    Ss+  20:46   0:00 -bash
xxx         3980  0.0  0.2  20044  5344 pts/1    Ss   22:13   0:00 -bash
xxx         4120  0.0  0.1  17748  2332 pts/1    S+   22:28   0:00 grep bash
```

可以看到 子进程 执行完之后被回收 

也可以使用两个子进程进行通信，由父进程 回收子进程



## 查看管道容量

 在这里，我们可以通过 `ulimit -a` 命令来查看系统页面上管道容量的大小，如下：

```
xxx@xxx-virtual-machine:~$ ulimit -a
real-time non-blocking time  (microseconds, -R) unlimited
core file size              (blocks, -c) 0
data seg size               (kbytes, -d) unlimited
scheduling priority                 (-e) 0
file size                   (blocks, -f) unlimited
pending signals                     (-i) 7243
max locked memory           (kbytes, -l) 240456
max memory size             (kbytes, -m) unlimited
open files                          (-n) 1024
pipe size                (512 bytes, -p) 8
POSIX message queues         (bytes, -q) 819200
real-time priority                  (-r) 0
stack size                  (kbytes, -s) 8192
cpu time                   (seconds, -t) unlimited
max user processes                  (-u) 7243
virtual memory              (kbytes, -v) unlimited
file locks                          (-x) unlimited

```

这里显示的是    8* 512  = 4096 个比特。

但实际上是  65535 个比特，可用下面的程序测试最大容量

```
#include<stdio.h>
#include<unistd.h>
#include<fcntl.h>
#include<stdlib.h>
#include<errno.h>
#include<signal.h>
int main()
{
 int pipefd[2];
 if(pipe(pipefd) < 0)
 {
  perror("pipe");
  return -1;
 }
 int ret;
 int size = 0;
 int flags = fcntl(pipefd[1], F_GETFL);
 fcntl(pipefd[1], F_SETFL, flags | O_NONBLOCK); // 设置为非阻塞 
 while (1) 
 {
  ret = write(pipefd[1], "c", 1); 
  if (ret < 0)
  {
   perror("write"); 
   break;
  }
  size++;
 }
  printf("size=%d\n", size);
  return 0; 
}

```

```
xxx@xxx-virtual-machine:~$ ./pipe
write: Resource temporarily unavailable
size=65536
```

## PIPE_BUF与[原子性](https://so.csdn.net/so/search?q=原子性&spm=1001.2101.3001.7020)问题



 POSIX 规定，小于 PIPE_BUF 的写操作必须是原子的：要写的数据应被连续地写到管道；大于 PIPE_BUF 的写操作可能是非原子的：内核可能会将数据与其它进程写入的数据交织在一起。POSIX 规定 PIPE_BUF 至少为512字节（Linux 中为4096字节），具体的语义如下：（其中n为要写的字节数）

**n <= PIPE_BUF，O_NONBLOCK disable**
        写入具有原子性。如果没有足够的空间供 n 个字节全部立即写入，则阻塞直到有足够空间将n个字节全部写入管道。
**n <= PIPE_BUF，O_NONBLOCK enable**
        写入具有原子性。如果有足够的空间写入 n 个字节，则 write 立即成功返回，并写入所有 n 个字节；否则一个都不写入，write 返回错误，并将 errno 设置为 EAGAIN。
**n > PIPE_BUF，O_NONBLOCK disable**
        写入不具有原子性。可能会和其它的写进程交替写，直到将 n 个字节全部写入才返回，否则阻塞等待写入。
**n > PIPE_BUF，O_NONBLOCK enable**
        写入不具有原子性。如果管道已满，则写入失败，write 返回错误，并将 errno 设置为 EAGAIN；否则，可以写入 1 ~ n 个字节，即部分写入，此时 write 返回实际写入的字节数，并且写入这些字节时可能与其他进程交错写入。

------

#### 示例 一      n <= PIPE_BUF，O_NONBLOCK disable

```c
#include <stdio.h>                                                                                                                     
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
         
#define SIZE 64*1024 //64KB
         
int main() {
    char A[SIZE];
    char B[SIZE];
         
    memset(A, 'A', sizeof(A));
    memset(B, 'B', sizeof(B));
         
    int pipefd[2];    
    if(pipe(pipefd) < 0) {
        perror("pipe error");    
        return -1;    
    }    
         
    int ret = 0;
         
    pid_t pid = fork();
    if(pid < 0 ) {
        perror("fork error");
        return -1;
    }
    else if(pid == 0) { //子进程1写入A    
        close(pipefd[0]);    
        ret = write(pipefd[1], A, sizeof(A)); //阻塞写入，直到64kB的数据全部写完才返回    
        printf("Child process [%d] wrote %d bytes of character A to the pipeline.\n", getpid(), ret);    
        exit(0);    
    }        
             
    pid = fork();    
    if(pid < 0 ) {    
        perror("fork error");    
        return -1;    
    }        
    else if(pid == 0) { //子进程2写入B    
        close(pipefd[0]);    
        ret = write(pipefd[1], B, sizeof(B)); //阻塞写入，直到64kB的数据全部写完才返回    
        printf("Child process [%d] wrote %d bytes of character B to the pipeline.\n", getpid(), ret);    
        exit(0);    
    }        
             
    close(pipefd[1]);    
    sleep(1); //休眠1s，确保父子进程的写端都已关闭    
    int n = 0;    
    while(1) {    
        char buf[4*1024] = {0};    
        ret = read(pipefd[0], buf, sizeof(buf)); //每次读取4KB数据    
        if(ret == 0) { //若读完数据，则不再读取    
            printf("Pipeline data has been read out.\n");    
            break;    
        }    
        printf("%2d: Parent process [%d] read %d bytes from the pipeline, buf[4095] = %c\n", ++n, getpid(), ret, buf[4095]);           
    }        
             
    return 0;    
}

```



```c
Child process [10597] wrote 65536 bytes of character A to the pipeline.////////////////////////////////////////////
 1: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = A
 2: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = A
 3: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = A
 4: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = A
 5: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = A
 6: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = A
 7: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = A
 8: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = A
 9: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = A
10: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = A
11: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = A
12: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = A
13: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = A
14: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = A
15: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = A
16: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = A
17: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = B
18: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = B
19: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = B
20: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = B
21: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = B
22: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = B
23: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = B
Child process [10598] wrote 65536 bytes of character B to the pipeline.////////////////////////////////////////////
24: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = B
25: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = B
26: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = B
27: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = B
28: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = B
29: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = B
30: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = B
31: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = B
32: Parent process [10596] read 4096 bytes from the pipeline, buf[4095] = B
Pipeline data has been read out.
```

     在上述代码中，我们让两个子进程都分别写入64KB的数据，由于从 Linux 2.6.11 以来，管道容量为65536字节，所以此时写入的数据与PIPE_BUF相等。在默认阻塞模式下，每个子进程完全写入64KB数据才能返回，而父进程对管道进行阻塞式读取，当没有数据时就一直阻塞等待，直到有数据到达。
        从执行结果来看，在这种情况下，子进程1先向管道中连续写入了64KB的数据A，接着父进程从管道中读取这些数据，但此时子进程2还不能向管道中写入，因为管道的剩余空间还无法一次容纳64KB数据，必须等到父进程读取完管道中剩余的所有数据才能开始写入。父进程读取完子进程1写入的数据后，管道中已经没有数据可读，父进程则开始进入阻塞等待状态，此时子进程2开始一次连续地写入64KB的数据B，之后，管道中又有数据了，父进程继续读取管道中子进程2写入的数据，直到读取完后，程序退出。
        分析结果我们发现，这两个子进程写入的数据都是连续地写入到管道中，并没有产生和其他进程交替写入的问题，所以由此可见，在 n <= PIPE_BUF的情况下，Linux将保证写入的原子性。
#### **【示例二】**：n > PIPE_BUF，O_NONBLOCK disable

```c
#include <stdio.h>                                                                                                                     
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
         
#define SIZE 68*1024 //68KB
         
int main() {
    char A[SIZE];
    char B[SIZE];
         
    memset(A, 'A', sizeof(A));
    memset(B, 'B', sizeof(B));
         
    int pipefd[2];    
    if(pipe(pipefd) < 0) {
        perror("pipe error");    
        return -1;    
    }    
         
    int ret = 0;
         
    pid_t pid = fork();
    if(pid < 0 ) {
        perror("fork error");
        return -1;
    }
    else if(pid == 0) { //子进程1写入A    
        close(pipefd[0]);    
        ret = write(pipefd[1], A, sizeof(A)); //阻塞写入，直到68kB的数据全部写完才返回    
        printf("Child process [%d] wrote %d bytes of character A to the pipeline.\n", getpid(), ret);    
        exit(0);    
    }        
             
    pid = fork();    
    if(pid < 0 ) {    
        perror("fork error");    
        return -1;    
    }        
    else if(pid == 0) { //子进程2写入B    
        close(pipefd[0]);    
        ret = write(pipefd[1], B, sizeof(B)); //阻塞写入，直到68kB的数据全部写完才返回    
        printf("Child process [%d] wrote %d bytes of character B to the pipeline.\n", getpid(), ret);    
        exit(0);    
    }        
             
    close(pipefd[1]);    
    sleep(1); //休眠1s，确保父子进程的写端都已关闭    
    int n = 0;    
    while(1) {    
        char buf[4*1024] = {0};    
        ret = read(pipefd[0], buf, sizeof(buf)); //每次读取4KB数据    
        if(ret == 0) { //若读完数据，则不再读取    
            printf("Pipeline data has been read out.\n");    
            break;    
        }    
        printf("%2d: Parent process [%d] read %d bytes from the pipeline, buf[4095] = %c\n", ++n, getpid(), ret, buf[4095]);           
    }        
             
    return 0;    
}
```

```
【分析】：
        在上述代码中，我们将示例一两个子进程写入的数据量由64KB提高到了68KB，此时写入的数据大于 PIPE_BUF。
        从执行结果来看，在这种情况下，子进程1先向管道中写入了64KB的数据A，待父进程读取完这些数据后，子进程2又向管道中写入了64KB的数据B，又待父进程读取完这些数据后，子进程1将剩余的4KB数据A写入，此时子进程1已将68KB的数据写完，紧接着，子进程2也将剩余的4KB数据B写入，此时子进程2同样也将68KB的数据写完，之后，父进程将管道中剩余的数据分两次读完，程序退出。
        分析结果我们发现，这两个子进程写入的数据并非连续地写入到管道中，而是两个进程交替写入（子进程1写入64KB数据 -> 子进程2写入64KB数据 -> 子进程1写入4KB数据 -> 子进程2写入4KB数据），所以由此可见，在 n > PIPE_BUF的情况下，Linux将不再保证写入的原子性。

```

总结：

```
当要写入的数据量不大于 PIPE_BUF 时，Linux将保证写入的原子性；
当要写入的数据量大于 PIPE_BUF 时，Linux将不再保证写入的原子性。
```



## 创建双向通信

```c
#include<stdlib.h>
#include<stdio.h>
#include<string.h>
#include<unistd.h>
#include<errno.h>
#define handle_error(msg) {perror(msg);exit(1);}

int main()
{
    int fd[2];
    int fd2[2];
    int retnum;
    const char*data ="testone";
    const char*data2 ="testtwo";
    if((pipe(fd)==-1)||(pipe(fd2)==-1))
    {
        handle_error("pipe");

    }
    pid_t  pid=fork();
    if(pid==-1)
    {

        handle_error("fork");

    }
    if(pid==0)  //子
    {
        close(fd[0]);
        close(fd2[1]);
        sleep(3);
        printf("child pid:%d: ppid:%d\n",getpid(),getppid());
        char read_buf[100]={0};
        char write_buf[100]={0};
        printf("child  process:\n");
        //write
        if(retnum=write(fd[1],data,strlen(data)))
        {


            printf("child write retnum:%d,write chararcters  %s\n",retnum,data);

        }
        //read
        if(retnum=	read(fd2[0],read_buf,100))
        {


            printf("child receive retnum:%d,recevie chararcters  %s\n",retnum,read_buf);

        }
        printf("in child process : %s",read_buf);
        close(fd2[0]);
        exit(1);
    }
    if(pid>0)
    {
        close(fd[1]);
        close(fd2[0]);

        sleep(1);
        printf("parent pid:%d:  ppid :%d\n",getpid(),getppid());
        char read_buf2[100]={0};
        //		char write_buf2[100]={0};
              if(retnum=  read(fd[0],read_buf2,100))
        {
            printf("parent receive retnum:%d,recevie chararcters  %s\n",retnum,read_buf2);
        }
        printf("in  parent  process : %s",read_buf2);
        close(fd[0]);

        printf("parent  process:\n");
        //		scanf("%s",write_buf2);
        //write
              if(retnum=  write(fd2[1],data2,strlen(data2)))
        {
            printf(" parent write retnum:%d,write chararcters  %s\n",retnum,data2);
        }

        //read
        close(fd2[1]);
        exit(1);

    }
    return 0;}

```

创建了 两根管道 ，fd  是  子写 父读    fd2 是 子读 父写

运行结果：

```c
parent pid:7430:  ppid :6003
child pid:7431: ppid:7430
child  process:
child write retnum:7,write chararcters  testone
parent receive retnum:7,recevie chararcters  testone
in  parent  process : testoneparent  process:
 parent write retnum:7,write chararcters  testtwo
child receive retnum:7,recevie chararcters  testtwo

```

# 有名管道的创建和使用fifo通信

使用命令创建 

```c
mkfifo   myfifo
```

使用函数创建

```
int mkfifo(const char *pathname, mode_t mode);
```

## 返回值

成功返回0，失败返回-1并设置errno

## 参数

pathname 命名管道路径
创建权限是(mode & ~umask)的[有名管道](https://so.csdn.net/so/search?q=有名管道&spm=1001.2101.3001.7020)文件      

## 目的

解决没有血缘关系之间的进程的通信

文件类型

```
prw-rw-r-- 1 toucheddog toucheddog        0 Nov  7 15:27 mfifo
```

该文件的类型时p，可以在文件目录中看到，大小始终是0

### 使用 fifo

```c
int fd1,fd2;
char buf[1024];
fd1 = open("管道名称",O_WRONLY);//读管道
fd2 = open("管道名称",O_RDONLY);//写管道

write(fd1,"内容",strlen("内容"));
read(fd2,buf,1024);//一次性取1KB数据
```

创建写文件   fifo_w.c   和读文件   fifo_r.c    和  管道  myfifo

```c
#include <string.h>
#include <unistd.h>
#include<sys/types.h>
#include<sys/stat.h>
#include<fcntl.h>
int main(int argc,char *argv[])

{

	if(argc<2)
	{
		printf("./a.out fifo name\n");
		exit(1);
	}
	int fd=open(argv[1],O_WRONLY);
	//写
	char buf[256];
	int num=1;
	while(1)
	{

		memset(buf,0,sizeof(buf));
		sprintf(buf,"xiaoming%04d",num++);
		write(fd,buf,strlen(buf));
		sleep(1);
	}
	close(fd);
	return 0;
}

```



```c
#include <stdio.h>
#include <unistd.h>
#include<sys/types.h>
#include <fcntl.h>
#include<sys/stat.h>
#include<string.h>
int main(int argc,char **argv)
{

	if(argc<2)
	{

		printf("./a.out  fifoname\n");
		return -1;
	}

	int fd =open(argv[1],O_RDONLY);
	char buf[256];
	int ret; 
	while(1)
	{
		ret=read(fd,buf,sizeof(buf));
		if(ret>0)
		{
			printf("read:%s\n",buf);

		}

	}
	close(fd);
	return 0;

}

```



```
 mkfifo   myfifo
```

执行的时候  将  管道名作为参数传入

写端

```
./fifo_w   myfifo
```

读端 

```
./fifo_r   myfifo
```

**注意：  我们的读端和写端可以有多个，也就是可以在多个不同终端 ，执行写端和读端代码   。**

创建的有名管道缓存的内容是不会存在磁盘中的。他只是在内核中的缓存中。

# 创建 mmap  通信

### 为什么创建mmap通信

所有的系统资源管理都是在内核空间中完成的。比如读写磁盘文件，分配回收内存，从网络接口读写数据等等。
用户空间通过系统调用让内核空问完成这些功能。

### **传统的数据传输：**

![image-20220720164356748](C:\Users\User\Desktop\image-20220720164356748.png)

1   用户进程通过方法向操作系统发起调用（上下文用户态转内核态）2 DMA 控制器把数据从硬盘中拷贝到读缓冲区。3 CPU 把读缓冲区数据拷贝到应用缓冲区（上下文由内核态转用户态返回）。4 用户进程通过方法发起调用（上下文用户态转内核态）。5CPU 将应用缓冲区数据拷贝到socket缓冲区  6 DMA控制器把数据从scoket 缓冲区拷贝到网卡（上下文从内核态切换回用户态返回）。

![image-20220720171134941](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20220720171134941.png)

用户空间是用户进程的运行空间 ，内核空间就是内核的运行空间

进程运行在内核空间就是内核态（安全起见，他们是相互隔离的）

**一次简单的IO过程产生了四次上下文切换和  四次CPU 拷贝。**

对于IO操作而言 都是cpu 发出相应的指令完成，但是相比CPU 来说，IO速度太慢了，因此就有了DMA(直接访存)，减少cpu等待时间

但无论谁来拷贝 ，频繁的拷贝耗时也是对性能的影响



![image-20220720165015748](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20220720165015748.png)

### 零拷贝技术

零拷贝是指计算机在执行操作时，cpu不需要先将数据从某处内存复制到另一个特定区域，这种技术通常用于通过网络传输文件时节省CPU周期和内存带宽。 注意并非不是没有数据拷贝的过程，只不过是减少用户态和内核态的切换次数及cpu拷贝的次数

mmap +write

主要实现方式 是将读缓冲区的地址和用户缓冲区的地址进行映射（内核缓冲区和应用缓冲区共享，从而减少从读缓冲区到用户缓冲区的一次cpu拷贝），

替换了write+read 的read操作，减少了一次cpu拷贝

![image-20220720172325159](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20220720172325159.png)



1   用户进程通过方法向操作系统发起调用（上下文用户态转内核态）2 DMA 控制器把数据从硬盘中拷贝到读缓冲区。3 上下文从内核态转用户态，mmap调用返回。4 用户进程通过方法发起调用（上下文用户态转内核态）。5CPU 将应用缓冲区数据拷贝到socket缓冲区  6 DMA控制器把数据从scoket 缓冲区拷贝到网卡（上下文从内核态切换回用户态返回）。

整个过程发生了4次用户态和内核态的上下文切换和3次拷贝

同时由于用户进程中的内存是虚拟的，只是映射到内核的读缓冲区，所以可以节省一半的内存空间，适合大文件的传输

### 其他的零拷贝技术（

```
#include <sys/sendfile.h>
ssize_ t sendfile(int out_ fd, int in_ fd, off _t *offset, size_ t count); 
```

减少了一次cpu 拷贝 和两次内存切换（数据直接在内核空间中使用，避免 用户空间到内核空间的拷贝）

![image-20220720173955349](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20220720173955349.png)

整个过程发生了2次用户态和内核态的上下文切换和3次拷贝

1   用户进程通过方法向操作系统发起调用（上下文用户态转内核态）2 DMA 控制器把数据从硬盘中拷贝到读缓冲区。 3

CPU 将应用缓冲区数据拷贝到socket缓冲区  6 DMA控制器把数据从scoket 缓冲区拷贝到网卡（上下文从内核态切换回用户态返回）。

只适用于完全不需要用户空间处理的情况，比如静态文件服务器

）

### mmap 采用的方法

Linux通过将一个虚拟内存区域与**一个磁盘上的对象**关联起来，以初始化这个虚拟内存区域的内容，这个过
程称为内存映射(memory mapping)。

![image-20220720165122153](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20220720165122153.png)



### mmap 函数介绍

```c
       #include <sys/mman.h>

       void *mmap(void *addr, size_t length, int prot, int flags,int fd, off_t offset); 
      
       int munmap(void *addr, size_t length);    // 回收创建的映射区
  //mmap函数成功返回相应的起始地址，失败返回-1。

 // 同理：解除映射应该使用：int munmap(void *start, size_t length); 成功返回0，失败返回-1.
```

#### 参数介绍

**参数start：**指向欲映射的内存起始地址，通常设为 NULL，代表让系统自动选定地址，映射成功后返回该地址。

**参数length：**代表将文件中多大的部分映射到内存。

参数prot：映射区域的保护方式。可以为以下几种方式的组合：
PROT_EXEC 映射区域可被执行
PROT_READ 映射区域可被读取
PROT_WRITE 映射区域可被写入
PROT_NONE 映射区域不能存取

**参数flags**：影响映射区域的各种特性。在调用mmap()时必须要指定MAP_SHARED 或MAP_PRIVATE。
MAP_FIXED 如果参数start所指的地址无法成功建立映射时，则放弃映射，不对地址做修正。通常不鼓励用此旗标。
MAP_SHARED对映射区域的写入数据会复制回文件内，而且允许其他映射该文件的进程共享。
MAP_PRIVATE 对映射区域的写入操作会产生一个映射文件的复制，即私人的“写入时复制”（copy on write）对此区域作的任何修改都不会写回原来的文件内容。
MAP_ANONYMOUS建立匿名映射。此时会忽略参数fd，不涉及文件，而且映射区域无法和其他进程共享。
MAP_DENYWRITE只允许对映射区域的写入操作，其他对文件直接写入的操作将会被拒绝。
MAP_LOCKED 将映射区域锁定住，这表示该区域不会被置换（swap）。

**参数fd**：要映射到内存中的文件描述符。如果使用匿名内存映射时，即flags中设置了MAP_ANONYMOUS  （可缩写为MAP_ANON），fd设为-1(fd可以忽略)。有些系统不支持匿名内存映射（unix），则可以使用fopen打开/dev/zero文件，然后对该文件进行映射，可以同样达到匿名内存映射的效果。

**参数offset**：文件映射的偏移量，通常设置为0，代表从文件最前方开始对应，offset必须是分页大小的整数倍。

------



#### **返回值：**

若映射成功则返回映射区的内存起始地址，否则返回MAP_FAILED(－1)，错误原因存于errno 中。

#### **错误代码：**

EBADF 参数fd 不是有效的文件描述词
EACCES 存取权限有误。如果是MAP_PRIVATE 情况下文件必须可读，使用MAP_SHARED则要有PROT_WRITE以及该文件要能写入。
EINVAL 参数start、length 或offset有一个不合法。
EAGAIN 文件被锁住，或是有太多内存被锁住。
ENOMEM 内存不足。

### 系统调用mmap()用于共享内存的两种方式：

#### （1）使用普通文件提供的内存映射：

适用于任何进程之间。此时，需要打开或创建一个文件，然后再调用mmap()

**典型调用代码如下：**

fd=open(name, flag, mode); if(fd<0) ...

ptr=mmap(NULL, len , PROT_READ|PROT_WRITE, MAP_SHARED , fd , 0);

通过mmap()实现共享内存的通信方式有许多特点和要注意的地方，可以参看UNIX网络编程第二卷。



```c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
#include <sys/mman.h>
int main(void) {
    char const* text = "Hello, World!";
    size_t size = strlen(text) * sizeof(
        text[0]);
    int fd = open("mmap.txt", O_RDWR | O_CREAT |
        O_TRUNC, 0644);
    if (fd == -1) {
        perror("open");
        return -1;
    }
    if (ftruncate(fd, size) == -1) {
        perror("ftruncate");
        return -1;
    }
    void* map = mmap(NULL, size, PROT_WRITE,
        MAP_SHARED, fd, 0);
    if (map == MAP_FAILED) {
        perror("mmap");
        return -1;
    }
    memcpy(map, text, size); // 写文件
    munmap(map, size);
    close(fd);
    return 0;
}
```

  大致原理如下：首先打卡一个"mmap.txt"文件。属性为可读可写没有则创建，存在则截断为0，文件权限为0644；后通过ftruncate函数将文件扩大到相应字符串长度大小。通过mmap对文件进行挂载，并且内存区域直接挂载到磁盘上。通过memcpy将字符串复制到相应的地址（写数据），并且取消挂载（munmap）。





#### （2）使用特殊文件提供匿名内存映射：

适用于具有亲缘关系的进程之间。由于父子进程特殊的亲缘关系，在父进程中先调用mmap()，然后调用 fork()。那么在调用fork()之后，子进程继承父进程匿名映射后的地址空间，同样也继承mmap()返回的地址，这样，父子进程就可以通过映射区 域进行通信了。注意，这里不是一般的继承关系。一般来说，子进程单独维护从父进程继承下来的一些变量。而mmap()返回的地址，却由父子进程共同维护。 对于具有亲缘关系的进程实现共享内存最好的方式应该是采用匿名内存映射的方式。此时，不必指定具体的文件，只要设置相应的标志即可。

### 

###### 单个进程的匿名映射并回收映射

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/mman.h>
int main(void) {
    char* psz = mmap(NULL, 8192,
        PROT_READ | PROT_WRITE,
        MAP_ANONYMOUS | MAP_PRIVATE, 0, 0);
    if (psz == MAP_FAILED) {
        perror("mmap");
        return -1;
    }
    sprintf(psz, "第一页");
    sprintf(psz + 4096, "第二页");
    printf("%s\n", psz);
    printf("%s\n", psz + 4096);
    if (munmap(psz, 4096) == -1) {
        perror("munmap");
        return -1;
    }
    //printf("%s\n", psz);
    printf("%s\n", psz + 4096);
    if (munmap(psz + 4096, 4096) == -1) {
        perror("munmap");
        return -1;
    }
    return 0;
}
```

代码较为简单：首先映射了8192字节（两页）的内存，并且直接保存在psz的指针中。权限为：可读可写，匿名映射（不需要文件），私有映射（内存文件修改时，磁盘文件不会修改）。

   后分别在第一页，第二页写入字符串，并且将其打印出来。并 取消挂在第一页内存，注释后的打印函数打印的正是已经取消挂载的内存页。如果任其运行则出现段错误。而后又打印了第二页内存，并将内存页取消挂载。

###### **父子进程的匿名映射**

```c
#include <unistd.h>
#include <sys/types.h>

#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

#include<sys/wait.h>
#include<stdio.h>
#include<stdlib.h>
#include <sys/mman.h>
int var=100; 
int main()
{
    int *point;

    pid_t pid;
    int fd=open("temp",O_RDWR|O_CREAT|O_TRUNC,0644);
    if(fd<0)
    {
        perror("open");
        exit(1);
    }
    unlink("temp");
    ftruncate(fd,4); //用于扩展文件大小
    point=(int *)mmap(NULL,4,PROT_READ|PROT_WRITE,MAP_SHARED,fd,0);
    //创建和fd 文件大小相同的映射区
    if(point==MAP_FAILED)  
    {
        perror("mmap error");
        exit(1);

    }
    close(fd);
    pid=fork() ;
    if(pid==0)
    {
        *point=2000;
        var=1000;
        printf("child,*point=%d,var=%d\n",*point,var);

    }
    else
    {

        sleep(1);
        printf("parent,*point=%d,var=%d\n",*point,var);
        wait(NULL);
        int ret=munmap(point,4);
        if(ret==-1)
        {
            perror("munmap error");
            exit(1);
        }
    }

    return 0;
}

```

**mmap 的 返回值是映射区的起始地址， 也就是代码中的point，   在point 到length 长度的这块区域内，写入的数据 在父子进程间是共享的。**

###### **非血缘关系的映射**

###### 写端

```c
#include</tou.h>
struct  STU{
	int id;
	char name[20];
	char sex;

} ;
/*void sys_err(char *str)
  {
  perror(str);
  exit(1);
  }
  */
int main(int argc,char**argv)
{
	int fd;
	struct STU student={10,"xiao bai",'M'};
	struct STU *mm; 

	if(argc<2)
	{
		printf("./a.out file_shared\n");
		exit(1);

	}
	fd=open(argv[1],O_RDWR|O_CREAT,0664);
	ftruncate(fd,sizeof(student));
	if(fd==-1)
	{
		perror("oppen error");
		exit(1);  
		//Osys_err("open error");
	}
	mm=mmap(NULL,sizeof(student),PROT_READ|PROT_WRITE,MAP_SHARED,fd,0);
	if(mm==MAP_FAILED)
	{
		//sys_err("mmap error:");
		perror("mmap error"); 
	}
	close(fd);
	while(1)
	{
		memcpy(mm,&student,sizeof(student));
		student.id++;
		sleep(1);
	}
	munmap(mm,sizeof(student)) ;
	return 0;


}

```

读端

```c
#include</tou.h>
struct  STU{
	int id;
	char name[20];
	char sex;

} ;
void sys_err(char *str)
{
	perror(str);
	exit(1);
}
int main(int argc,char**argv)
{
	int fd;
	struct STU student;
	struct STU *mm; 

	if(argc<2)
	{
		printf("./a.out file_shared\n");
		exit(1);

	}
	fd=open(argv[1],O_RDONLY);
	if(fd==-1)
	{
		sys_err("open error");
	}
	mm=mmap(NULL,sizeof(student),PROT_READ,MAP_SHARED,fd,0);
	if(mm==MAP_FAILED)
	{
		sys_err("mmap error");
		exit(1);
	}
	close(fd);
	while(1)
	{

		printf("id=%d\t name=%s\t sex=%c\n",mm->id,mm->name,mm->sex);
		sleep(1);
	}
	munmap(mm,sizeof(student)) ;
	return 0;


}

```

```
./mmap_w  filename
./mmap_r filename
```

这里是利用filename  创建和这个文件大小相同的映射区

### 注意：

总结:使用mmap时务必注意以下事项:
	1.创建映射区的过程中， 隐含着一次对映射文件的读操作。 所以必须要有PROT_READ

2. 当MAP_ _SHARED时， 要求:映射区的权限应<=文件打开的权限(出于对映射区的保护)。而MAP_ PRIVATE
则无所谓，因为mmap中的权限是对内存的限制。.
3. 映射区的释放与文件关闭无关。只要映射建立成功，文件可以立即关闭.(因为Linux通过将一个虚拟内存区域与**一个磁盘上的对象**关联起来，以初始化这个虚拟内存区域的内容)
4. 特别注意，当映射文件大小为0时，不能创建映射区。所以:用于映射的文件必须要有实际大小!!
mmap.使用时常常会出现总线错误，通常是由于共享文件存储空间大小引起的。
5. munmap 传入的地址- -定是mmap.的返回地址。坚决杜绝指针++操作。
6. 如果文件偏移量必须为4K的整数倍   
7. mmap 创建映射区出错概率非常高，- -定要检查返回值，确保映射区建立成功再进行后续操作。



# 使用 mmap  复制文件的程序

要求实现多进程拷贝

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <sys/types.h>
#include <fcntl.h>
#include <sys/stat.h>
#include <sys/mman.h>
#include <sys/wait.h>

int main(int argc,char *argv[])
{
    char *p;
    unsigned char *src,*dst;
    int fd1,fd2;
    int i,n = atoi(argv[3]);
    int size;
    int blocksize;

    pid_t pid;
    struct stat sta;



    fd1 = open(argv[1],O_RDONLY);
    //fd1 = open("wegbk.txt",O_RDONLY);
    if(fd1 < 0)
    {
        perror("open src failed.");
        exit(1);
    }
    fd2 = open(argv[2],O_RDWR|O_CREAT,0664);
    //fd2 = open("t.txt",O_RDWR|O_CREAT,0664);
    if(fd2 < 0)
    {
        perror("open dst failed.");
        close(fd1);
        exit(1);
    }

    if(fstat(fd1,&sta))
    {
        perror("Stat file error.");
        close(fd1);
        close(fd2);
        exit(1);
    }
    //printf("*****src file size = %d***\n",sta.st_size);
    //将目标文件扩展为源文件大小
    size = sta.st_size;
    ftruncate(fd2,size);
    blocksize = size/n;

    src = (unsigned char*)mmap(NULL,size,PROT_READ,MAP_SHARED,fd1,0);
    if(src == MAP_FAILED)
    {
        perror("map src error.");
        close(fd1);
        close(fd2);
        exit(1);
    }
    dst = (char*)mmap(NULL,sta.st_size,PROT_WRITE,MAP_SHARED,fd2,0);
    if(dst == MAP_FAILED)
    {
        perror("map dst error.");
        close(fd1);
        close(fd2);
        exit(1);
    }
    close(fd1);
    close(fd2);

    for(i=0;i<n;i++)
    {
        pid = fork();
        if(pid<0)
        {
            perror("fork error.");
            exit(1);
        }
        else if(pid == 0)
            break;
    }

    if(i<n)
    {
        //printf("%dth child\n",i+1);
        int k;
        if(i == (n-1)) //千万注意不能写成=，否则会搞死你的
            blocksize += (size%n);

        char buf[blocksize];
        src += (blocksize*i);
        dst += (blocksize*i);
        printf("%dth Chlild copying----start addr src=%u,dst = %u\n",i+1,src,dst);

        memcpy(buf,src,blocksize);
        memcpy(dst,buf,blocksize);
        printf("%dth Chlild copy done.\n",i+1);
    }
    else
    {
        //In parent
        //sleep(2);
        int j;
        for (j = 0; j < n; j++)
        {
            waitpid(-1, NULL, WNOHANG);
        }

        munmap(src,sta.st_size);
        munmap(dst,sta.st_size);
        return 0;
    }

    return 0;
}

```









