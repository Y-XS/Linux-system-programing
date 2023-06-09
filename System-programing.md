#  cmd

## dir

## chmod

## ps

```sh
ps ajx
ps aux
```



## find

+ -type：按文件类型搜索 d/p/s/c/b/l/f

  ```sh
  # ./ 表示当前目录
  # 默认递归查找，-maxdepth指定深度
  find ./ -maxdepth 1 -type f
  ```

+ -name：按文件名搜索

  ```sh
  find ./ -maxdepth 1 -name 'r*'
  ```

+ -maxdepth：指定搜索深度（默认做第一参数）

  ```sh
  find ./ -maxdepth 1 -type f
  ```

+ -size：按文件大小搜索. 单位：k、M、G

  ```sh
  find ./ -size +20M -size -50M
  ```

+ -atime、-mtime、-ctime：天

  -amin、-mmin、-cmin：分钟

+ -exec：将find的结果集执行某一指定命令。

  ```sh
  find ./ -type f -exec ls -l {} \;
  ```

+ -ok：以交互式的方式，将find搜索结果集执行某一指定命令

  ```sh
  find ./ -type f -ok ls -l {} \;
  ```

## grep

```sh
# -r：递归 -n：显示行号
# 递归检索当面目录
grep -r 'copy' ./ -n
# 检索指定文件
grep 'set' CMakeLists.txt -n
# 通过管道符将ps命令结果传给grep进行查找
ps aux | grep 'cupsd'
```

## xargs

> xargs默认以空格分割结果集单个项目
>
> 当结果集数量过大时，xargs可以对结果集进行分片映射

```sh
find ./ -maxdepth 1 -type f -exec ls -l {} \;
# 将find搜索的结果集执行某一指定命令。当结果集数量过大时，可以分片映射。下式不加xargs结果会出错。
find ./ -maxdepth 1 -type f | xargs ls -l
# -print0以0替代空格对结果集进行分割
find ./ -maxdepth 1 -type f -print0 | xargs -0 ls -l
```

## rpm

## yum

```sh
yum install sl
yum remove sl
yum list
yum search sl
yum update
yum update sl
```

## tar

+ 压缩

  ```sh
  # z：压缩方式 gzip
  # c：create
  # v：显示压缩过程
  # f：文件
  tar zcvf test.tar.gz file1 dir2 #使用gzip方式压缩
  tar jcvf test.tar.gz file1 dir2 #使用bzip2方式压缩
  ```

+ 解压

  ```sh
  tar zxvf test.tar.gz file1 dir2 #使用gzip方式解压缩
  tar jxvf test.tar.gz file1 dir2 #使用bzip2方式解压缩
  ```

  

# vim

## 命令

+ 插入：a 光标后、i 光标前、o 下一行
+ 撤销：u、ctrl+r
+ 删除（实为剪切）：
  + 单个字符：x
  + 一个单词(向后删除)：dw
  + d+home，d+end
  + 删除括号内容：di( 
  + 删除块：v+hjkl+d （v进入可视模式）
  + 删除行：dd，ndd（n行）
+ 复制粘贴：
  + 复制：yy
  + 粘贴：p
+ 查找替换：
  + /+sth，回车，n下一个
  + 光标选中按 *，再按下一个
  + 替换字符：r+s
+ 跳转：
  + 跳转文件首：gg
  + 跳转文件尾：G
  + 跳转指定行：88G、:88
  + 大括号跳转：%
+ 分屏：
  + 横分：sp
  + 竖分：vsp
+ 挪动光标：hjkl
+ 自动格式化程序：gg=G

## 配置



# gcc

> gcc用来编译c语言，g++编译c++

![](imgs/gcc.png)

+ -I：指定头文件所在目录位置

+ -o：指定输出文件名

+ -c：只做预处理、编译、汇编。得到二进制文件

+ -g：编译时添加调试语句。主要支持gdb调试

+ -Wall：显示所有警告信息

+ -D：向程序中“动态”注册宏定义

+ -l：指定动态库名

+ -L：指定动态库路径

  ```sh
  gcc hello.cpp -I ./include -o hello -g -Wall -D HELLO -lpthread
  ```

# lib

## 静态库

### 步骤

1. 将 .c 生成 .o 文件

   ```sh
   gcc -c add.c -o add.o
   ```

2. 使用 ar 工具制作静态库

   ```sh
   ar rcs lib[name].a add.o sub.o div.o
   ```

3. 编译静态库到可执行文件中

   ```sh
   gcc test.c lib[name].a -o 
   ```

### Template

![](imgs/static-lib-dir.png)

```sh
[root@ src] g++ -c add.cpp -o add.o
[root@ src] g++ -c sub.cpp -o sub.o
[root@ src] ar rcs libmath.a add.o sub.o
[root@ src] mv libmath.a *.o ../lib
[root@ lib] g++ test.cpp ./lib/libmath.a -o test -I ./inc
```

```c++
// test.cpp
#include<iostream>
#include"math.h"
using namespace std;
int main(){
        int a=9,b=5;
        cout<<"add("<<a<<","<<b<<") = "<<add(a,b)<<endl;
        cout<<"sub("<<a<<","<<b<<") = "<<sub(a,b)<<endl;
        return 0;
}
```

```c++
// math.h
#ifndef MATH_H
#define MATH_H
int add(int,int);
int sub(int,int);
#endif
```



## 动态库 

### 步骤

1. 将.c生成.o文件，（生成与位置无关的代码 -fPIC）

   ```sh
   [root@ src] g++ -c add.cpp -o add.o -fPIC
   ```

2. 使用 gcc -shared 制作动态库

   ```sh
   [root@ src] g++ -shared -o libmath.so add.o sub.o
   ```

3. 编译可执行程序时，指定所使用的动态库。-l：指定库名(去掉lib前缀和.so后缀) -L：指定库路径

   ```sh
   [root@ src] g++ test.cpp -o test -lmath -L./lib -I./inc
   ```

4. 运行可执行程序 ./test 出错！！！

   > error while loading shared 1ibraries: 1ibxxx.so: cannot open shared object file: No such file or directory

   + 原因：
     + 链接器：工作于链接阶段，工作时需要 -l 和 -L
     + 动态链接器：工作于程序运行阶段，工作时需要提供动态库所在目录位置
   + 解决方法：
     + [临时] 通过环境变量：export LD_LIBRARY_PATH=dir
     + [永久] 写入终端配置文件：
       1. vi ~/.bashrc
       2. 写入export LD_LIBRARY_PATH=dynamic_lib_dir
       3. . .bashrc   /   source .bashrc   /   重启终端
     + [永久] 拷贝自定义动态库到 /lib（标准C库所在目录）
     + [永久] 配置文件法：
       1. vi /etc/ld.so.conf
       2. 写入动态库绝对路径
       3. ldconfig -v 使配置文件生效

```sh
[root@ src] g++ -c add.cpp -o add.o -fPIC
[root@ src] g++ -c sub.cpp -o sub.o -fPIC
[root@ src] g++ -shared -o libmath.so add.o sub.o
[root@ src] mv libmath.so *.o ../lib
```



### Template



# gdb

基础指令：

+ -g：使用该参数编译可执行文件，得到调试表
+ gdb ./a.out 调试a.out
+ list：list 1 列出源码。根据源码行号设置断点
+ b：b 20 在20行设置断点
+ delete/d：删除断点
+ run/r：运行调试
+ next/n：下一步（越过函数）
+ step/s：下一步（进入函数）
+ print/p：p i 查看变量的值
+ continue：继续执行断点后续指令
+ quit：退出gdb调试

其他指令：

+ finish：结束当前函数调用
+ set args：设置main函数命令行参数
+ info b：查看断点信息表
+ b 20 if i=5：设置条件断点
+ ptype：查看变量类型
+ bt：列出当前程序存活的栈帧
+ frame：根据栈帧编号，切换栈帧
+ display：设置跟踪变量
+ undisplay：取消设置跟踪变量

gdb只能跟踪一个进程。可在**fork函数调用前**，通过指令设置gdb调试工具跟踪父进程或子进程。默认跟踪父进程。

```sh
set follow-fork-mode child #设置跟踪子进程
set follow-fork-mode parent #设置跟踪父进程
```

# makefile

## 基本语法

> 命名：makefile、Makefile
>
> 默认将第一个目标作为终极目标，all 可改变

+ 1个规则：

  ```makefile
  目标：依赖条件
  (tab) 命令
  ```

   	1. 目标的时间必须晚于依赖条件的时间，否则，更新目录
   	2. 依赖条件如果不存在，找寻新的规则去产生依赖

+ 2个函数：

  ```makefile
  #匹配当前目录下所有.c文件。将文件名组成列表，赋值给变量src
  src = $(wildcard *.c)
  #将参数3中，包含参数1的部分，替换为参数2（此处即将.c后缀替换为.o），并赋值给obj
  obj = $(patsubst %.c, %.o, $(src))
  ```

+ 3个自动变量：

  + $@：在规则的命令中，表示规则中的目标
  + $^：在规则的命令中，表示所有依赖条件
  + $<：在规则的命令中，表示第一个依赖条件。如果该变量应用在模式规则中，它可将依赖条件列表中的依赖依次取出，套用模式规则。

+ 模式规则：

  ```makefile
  %.o:%.c
  	g++ -c $< -o $@
  # 静态模式规则
  $(obj):%.o:%.c
  	g++ -c $< -o $@
  ```

+ 伪目标：

  ```makefile
  .PHONY: clean all
  ```

+ 参数：

  + -n：模拟执行make、make clean命令
  + -f：指定文件执行make命令

+ other：

  + all：指定 makefile 的终极目标

  + clean（没有依赖）

    ```makefile
    clean:
    	-rm -rf $(obj) a.out #‘-’作用是，删除不存在文件时，不报错，顺序执行结束
    ```

## Template

```makefile
src = $(wildcard *.c)
obj = $(patsubst %.c, %.o, $(src))

myArgs= -Wall -g
ALL:a.out

a.out: $(obj)
	g++ $^ -o $@ $(myArgs)
	
$(obj):%.o:%.c
	g++ -c $< -o $@ $(myArgs)
	
clean:
	-rm -rf $(obj) a.out
.PHONY: clean ALL
```

```makefile
src = $(wildcard ./src/*.cpp)
# obj = $(patsubst ./src/%.cpp, ./bin/%.o, $(src))

args = -Wall -g
all:demo.out

demo.out: $(src)
        g++ $^ -o ./bin/$@ $(args)

# $(obj):./bin/%.o:./src/%.cpp
#       g++ -c $< -o $@ $(args)

clean:
        -rm -rf $(obj) ./bin/demo.out
.PHONY: clean all
```



 

# ---系统编程---

```sh
# 安装man-pages
yum install man-pages
# 查看手册
man man
# 查看函数系统调用
man 2 open
```

# common

## 基础知识

![](imgs/inode.png)

+ inode

  其本质为结构体，存储文件的属性信息。如：权限、类型、大小、时间、用户、盘块位置……也叫作文件属性管理结构，大多数的 inode 都存储在磁盘上。
  少量常用、近期使用的inode会被缓存到内存中。

+ dentry

  **目录项**，其本质依然是结构体，重要成员变量有两个{文件名，inode，...}，而文件内容 (data) 保存在磁盘盘块中。

硬连接&软连接

硬连接：等于创建一个目录项



## 文件描述符

| ![](imgs/fd.png) | ![](imgs/fd2.png) |
| ---------------- | ----------------- |

> 进程地址空间 0-4 G

+ PCB进程控制块：本质是结构体

+ 成员：文件描述符表

+ 文件描述符：0/1/2/3/4.../1023（默认使用表中可用的最小值）

  + 0：STDIN_FILENO

  + 1：STDOUT_FILENO

  + 2：STDERR_FILENO



## 虚拟设备

```c++
//linux 虚拟设备
//1. /dev/null 文件黑洞 
//2. /dev/zero 无线空字符 
//3. /dev/random 产生随机数据依赖系统中断，速度较慢，但随机性好
//4. /dev/urandom 不依赖系统中断，数据产生速度快，但随机性较低
```





## 阻塞 / 非阻塞

> 阻塞/非阻塞：是设备文件、网络文件的属性

产生阻塞的场景。读**设备文件**。读**网络文件**。(读常规文件无阻塞概念)

```c++
// /dev/tty -- 终端文件
//设置 /dev/tty 为非阻塞状态（默认阻塞）
open("/dev/tty",O_RDWR|O_NONBLOCK);
```

## func

+ strerror

  ```c++
  #include<string.h>
  #include<errno.h>
  char* strerror(int errno);
  ```

+ perror

  ```c++
  void perror(const char* s);
  perror("perror test");
  ```

+ strace

  ```sh
  strace ./a.out
  # 命令：输出程序执行过程中的系统调用
  ```

+ fcntl

  ![](imgs/fcntl.png)

  ```c++
  #include<unistd.h>
  #include<fcntl.h>
  /*
  args:
  	fd：文件描述符
  	cmd：操作命令
  cmd:
  	F_DUPFD：Find the lowest numbered available file descriptor greater than or equal to arg and make it be a copy of fd.
  	F_GETFL
  	F_SETFL
  return:
  	取决于操作命令
  */
  int fcntl(int fd, int cmd, ...arg);
  ```

  ```c++
  //template
  int flags = fcntl(fd,F_GETFL);
  flags |= O_NONBLOCK;
  fcntl(STDIN_FILENO, F_SETFL, flags);
  ```

+ dup

  ```c++
  #include<unistd.h>
  /*
  args:
  	oldfd：原文件描述符
  	newfd：新文件描述符
  return:
  	success：new descriptor
  	fail：-1 & errno
  */
  int dup(int oldfd);
  // oldfd <- newfd
  int dup2(int oldfd,int newfd);
  ```
  
+ memcpy

  ```c++
  void * memcpy(void * destination, const void * source, size_t num);
  ```

+ memset

  ```c++
  void *memset(void *dest, int c, size_t count);
  ```

+ memmove

  ```c++
  void * memmove(void* destination, const void* source, size_t num);
  ```

+ memcmp

  ```c++
  int memcmp(const void * ptr1, const void* ptr2, size_t num);
  ```

  


# 文件IO

## open

```c++
#include<unistd.h>
/**
args: 
	pathname:文件路径名
	flags：文件打开方式 O_RDONLY|O_WRONLY|O_RDWR     O_CREAT|O_APPEND|O_TRUNC|O_EXCL|O_NONBLOCK
return：
	success: file descriptor
	fail:-1 & errno
**/
int open(char *pathname, int flags);
//	mode:参数2指定O_CREAT时使用此参数，用来指定文件权限，受umask影响，最终权限与umask相与
int open(char *pathname, int flags, mode_t mode);
```

```c++
//template
int fd = open(src_path,O_RDWR);
int fd = open(src_path,O_RDWR|O_CREAT,0777);
```

## close

```c++
int close(int fd);
```

## read / write

![](imgs/buf.png)

> 预读入缓输出

```c++
#include <unistd.h>
/**
args: 
	fd：文件描述符
	buf：缓冲区
	count：字节数（bytes）
return：
	n: 读取的字节数
	0：indicates end of file
	-1：error & errno. if errno==EAGIN | EWOULDBLOCK则考虑非阻塞时无数据		情况
**/
ssize_t read(int fd, void *buf, size_t count);
//template
int n = read(fd1,buf,1024);
```

```c++
#include <unistd.h>
/**
args: 
	fd：文件描述符
	buf：缓冲区
	count：字节数（bytes）
return：
	n: 写入的字节数
	-1：error & errno
**/
ssize_t write(int fd, const void *buf, size_t count);
//template
write(fd2,buf,n);
```

## lseek

> 文件读写使用同一偏移位置

```c++
/*
args:
	fd：文件描述符
	offset：偏移量
	whence：起始偏移位置：SEEK_SET/SEEK_CUR/SEEK_END
return:
	success：较起始位置偏移量
	fail：-1 & errno
application:
	1.读写使用同一偏移位置
	2.使用lseek获取拓展文件大小(可用stat()替代)
	3.使用lseek拓展文件大小(想要真正拓展，必须引起IO操作，可用truncate())
*/
off_t lseek(int fd, off_t offset, int whence);
```

## stat

```c++
/*
args:
	path：文件路径
	buf：存放文件属性
return:
	success：0
	fail：-1 & errno
other：
	符号穿透：stat会，lstat不会
*/
int stat(const char* path,struct stat *buf);
struct stat{
    st_size,//获取文件大小
    st_mode,//获取文件类型
    ...
}
```

## link/unlink

+ link：可以为已存在的文件创建目录项（硬链接）

  ```c++
  /*
  args:
  	oldpath：旧文件路径
  	newpath：新文件路径
  return:
  	success：0
  	fail：-1 & errno
  other：
  	mv命令即是修改了目录项，而并不修改文件本身
  */
  int link(const* oldpath,const char* newpath);
  ```

+ unlink：删除一个文件的目录项

  ```c++
  /*
  args:
  	pathname：文件路径
  return:
  	success：0
  	fail：-1 & errno
  note：
  	unlink函数的特征:清除文件时，如果文件的硬链接数到0了，没有dentry对应，但该文件仍不会马上被释放。要等到所有打开该文件的进程关闭该文件，系统才会挑时间将该文件释放掉。
  */
  int unlink(const char* pathname);
  ```

+ 隐式回收：当进程结束运行时，所有该进程打开的文件会被关闭，申请的内存空间会被释放。系统的这一特性称之为隐式回收系统资源。

# dir

注意：目录文件也是“文件”。其文件内容是该目录下所有子文件的目录项dentry。可以尝试用vim打开一个目录。   

## opendir

```c++
/*
args:
	name：目录名
return:
	success：0
	fail：-1 & errno
*/
DIR* opendir(char* name);
```

## closedir

```c++
/*
args:
	dp：目录指针
return:
	success：0
	fail：-1 & errno
*/
DIR* closedir(DIR* dp);
```

## readdir

```c++
/*
args:
	dp：目录指针
return:
	success：0
	fail：-1 & errno
*/
struct dirent* readdir(DIR* dp);
struct dirent{
    inode
    char dname[256];
}
```

# 进程

> kill -l
>
> 进程中可用 perror 打印错误信息

```c++
//进程中可用 perror 打印错误信息
perror("xxx error");
```

## template

```c
//process template
pid_t pid;
int i = 0;

pid = fork();
if(pid<0){//error
}else if(pid == 0){//子进程
}else{//主进程
}
```



## 内存映射

![](imgs/RAM.png)

## PCB

每个进程在内核中都有一个**进程控制块(PCB)**来维护进程相关的信息，Linux内核的进程控制块是 task_struct 结构体。

PCB进程控制块：

+  进程id
+ 文件描述符表
+ 进程状态：初始态、就绪态、运行态、挂起态、终止态
+ 进程工作目录：
+ *umask掩码
+ 信号相关信息资源
+ 用户id和组id

## 环境变量

```sh
# 查看所有环境变量
env
```

## fork

```c++
/*
args: void
return:
	success：父进程返回子进程pid、子进程返回0
	fail：-1 & errno
note：
	通过返回值判断当前进程
*/
pid_t fork(void);
//获取当前进程pid
pid_t getpid(void);
//获取父进程pid
pid_t getppid(void);
```

注：

+ fork后进程执行顺序不确定，取决于内核所用的调度算法
+ 在子进程中使用 getppid 返回1，是由于父进程先退出，造成子进程被 init（id=1）接管



## 进程共享

父子进程间遵循**读时共享写时复制**原则

+ 相同

  刚fork后：data段、text段、堆、栈、环境变量、全局变量、宿主目录位置、进程工作目录位置、信号处理方式

+ 不同

  进程id、返回值、各自的父进程、进程创建时间、闹钟、未决信号集

+ 共享

  1. 文件描述符
  2. mmap映射区

## exec

```c++
/*
args: 
return:
	只有失败时才返回
	fail：-1 & errno
note：
	l(list)：命令行参数列表
	p(path)：搜索file时使用path变量
	v(vector)：使用命令行参数数组
	e(environment)：使用环境变量数组，不使用进程原有的环境变量，设置新加载程序运行的环境变量
*/
int execl(const char* path,const char* arg,...);
int execlp(const char* file,const char* arg,...);
int execle(const char* path,const char* arg,...,char* const envp[]);
int execv(const char* path,char* const argv[]);
int execvp(const char* file,char* const argv[]);
int execve(const char* path,char* const argv[],char* const envp[]);

//template
//此函数参数从argv[0]开始算，因此要写两遍“ls”，第一遍是文件名，第二遍是命令行参数
execlp("ls","ls","-l",NULL);
```

##  回收子进程

+ 孤儿进程

  孤儿进程：父进程先于子进程结束，则子进程成为孤儿进程，init进程成为子进程的父进程，称为init进程 (进程孤儿院) 领养孤儿进程。

+ 僵尸进程

  僵尸进程：进程终止，父进程尚未回收，子进程残留资源（PCB）存放于内核中，变成僵尸(zombie）进程。

  特别注意，僵尸进程是不能使用kill命令清除掉的。因为kill命令只是用来终止进程的,而僵尸进程已经终止。此时，需要杀死父进程，子进程被init进程接管进行清理工作。

+ wait

  + 阻塞等待子进程退出
  + 清理子进程残留在内核的pcb资源
  + 通过参数传出子进程结束状态

  ```c++
  #include <sys/wait.h>
  //详见 man 2 wait
  // 一次wait/waitpid函数调用，只能回收一个子进程
  // status 可通过宏函数查询子进程终止信息，如WIFEXITED、WIFSIGNALED等
  pid_t wait(int *status);
  
  int waitpid(idtype_t idtype, id_t id, siginfo_t *infop, int options);
  ```

  ```c++
  //template
  int status;
  pid_t pid = fork();
  if(pid == 0){
  	cout<<"=====sub====="<<endl;
  	cout<<"pid: "<<getpid()<<" ppid: "<<getppid()<<endl;
  	sleep(15);
  	return 66;
  }else if (pid > 0){
  	cout<<"=====parent====="<<endl;
  	cout<<"pid: "<<getpid()<<" ppid: "<<getppid()<<" subid: "<<pid<<endl;
  	cout<<"waiting..."<<endl;
      // wait...
  	pid_t wpid = wait(&status);
  	if(WIFEXITED(status)){
  		cout<<"sub process exit normally "<<WEXITSTATUS(status)<<endl;
  	}else if (WIFSIGNALED(status)){
          //termsig：kill -9 sub_pid
  		cout<<"sub process exit with termsig: "<<WTERMSIG(status)<<endl;
  	}	
  }else if (pid==-1){
  	perror("create sub process failed");
  }
  return 0;
  ```

+ waitpid

  ```c++
  #include <sys/wait.h>
  /*
  args: 
  	pid：指定回收的子进程pid
  	status：传出进程回收状态
  	options：WNOHANG指定为非阻塞回收
  return:
  	>0：返回成功回收的子进程pid
  	0：函数调用时，参3指定了WNOHANG， 并且，没有子进程结束。
  	-1：fail & errno
  */
  pid_t waitpid(pid_t pid, int *status, int options);
  ```
  
  ```c++
  //template
  int status,i=0;
  pid_t pid,wpid;
  
  for(i;i<5;i++){
  	pid = fork();
  	//子进程不进循环
  	if(pid == 0){
  		break;
  	}
  }
  //父进程
  if(i==5){
      //阻塞方式
      /*
  	while ((wpid = waitpid(-1,NULL,0))){
  		cout<<"wait for pid "<<wpid<<endl;
  	}*/
  	//非阻塞方式
  	while ((wpid = waitpid(-1,NULL,WNOHANG))!=-1)
  	{
  		if(wpid>0){
  			cout<<"wait for pid "<<wpid<<endl;
  		}else if (wpid == 0)
  		{
  			sleep(1);
  			continue;
  		}
  	}
  }else{	//子进程
  	sleep(i);
  	cout<<"i am "<<i+1<<"th child pid="<<getpid()<<endl;
  }
  return 0;
  ```
  



## 进程间通信IPC

> inter-process communication

常用的进程间通信方式有：

+ 管道（使用最简单）
+ 信号（开销最小）
+ 共享映射区mmap（无血缘关系）
+ 本地套接字socket（最稳定）



### 管道PIPE

> 确保关闭多余读写端，保证数据单向流通

实现原理：内核借助环形队列机制，使用内核缓冲区实现。

特质：**伪文件**、管道中的数据只能一次读取、**数据单向流动**

局限性：不能自己写自己读、数据不可反复读、半双工通信、血缘关系进程间可用 

![](imgs/pipe.png)

```c++
/*
args: 
	fd[0]：读端
	fd[1]：写端
return:
	0：成功
	-1：fail & errno
*/
int pipe(int fd[2]);
```

```c++
//template
int ret;
int fd[2];
pid_t pid;
char* str = "hello pipe\n";
char buf[1024];
ret = pipe(fd);
if(ret==-1)
    sys_err("pipe error");
pid = fork();//创建子进程时，子进程也有两个文件描述符指向同一管道
if(pid>0){
    close(fd[0]);//父进程关闭读端
    write(fd[1],str,strlen(str));
    sleep(1);
    close(fd[1]);
}else if(pid==0){
    close(fd[1]);//子进程关闭写端
    ret = read(fd[0],buf,sizeof(buf));
    write(STDOUT_FILENO,buf,ret);
    close(fd[0]);
}
//practice
//使用管道实现父子进程间通信，完成: ls | wc -l。假定父进程实现 ls，子进程实现 wc
//使用管道实现兄弟进程间通信，完成: ls | wc -l。假定兄进程实现 ls，弟进程实现 wc
```

管道读写行为：

+ 读管道：
  1. 管道有数据：read返回实际读到的字节数
  2. 管道无数据：
     1. 无写端：read返回0（类似读到文件尾）
     2. 有写端：read阻塞等待
+ 写管道：
  1. 无读端：异常终止（SIGPIPE）
  2. 有读端：
     1. 管道已满，阻塞等待
     2. 管道未满，返回写出的字节个数

管道缓冲区大小：

```c++
//可用ulimit -a 命令查看当前系统中创建管道文件所对应的内核缓冲区大小。通常为:
pipe size	(512 bytes,-p) 8
//也可以使用fpathconf函数，借助参数选项来查看。使用该宏应引入头文件<unistd.h>
/*
args: 
	fd：
	name：
return:
	success：返回管道的大小
	fail：-1 & errno
*/
long fpathconf(int fd, int name);
```

### 命名管道FIFO

```c++
#include <sys/stat.h>
/*
args: 
	pathname：管道路径
	mode：文件权限
return:
	success：0
	fail：-1 & errno
*/
//创建命名管道后，即可像普通文件一样对该管道进行open、read、write
int mkfifo(const char *pathname, mode_t mode);
```



### 共享存储映射mmap

> mmap容易出错，注意校验返回值

```c++
#include <sys/mman.h>
/*
args: 
	addr：指定映射区的首地址。通常传NULL，表示让系统自动分配
    length：共享内存映射区的大小。(<=文件的实际大小)
    prot：共享内存映射区的读写属性。PROT_READ、PROT_WRITE、PROT_READ|PROT_WRITE
    flags：标注共享内存的共享属性。MAP_SHARED、MAP_PRIVATE、MAP_ANONYMOUS(匿名映射)
    fd：用于创建共享内存映射区的那个文件的文件描述符
    offset：默认0，表示映射文件全部。偏移位置。需是4k的整数倍
return:
	success：映射区的首地址
	fail：MAP_FAILED（void*(-1)） & errno
*/
void *mmap(void *addr,size_t length,int prot,int flags,int fd,off_t offset);//创建共享内存映射
/*
args: 
	addr：mmap返回的地址
    1ength：共享内存映射区的大小。(<=文件的实际大小)
return:
	success：0
	fail：-1 & errno
*/
int munmap(void *addr,size_t length);
```

```c++
//template
//1.父子进程间mmap通信
int var=100;
int main(){
    int *p;
    pid_t pid;
    int fd;
    fd = open("temp",O_RDWR|O_CREAT|O_TRUNC,0644);
    ftruncate(fd,4);
    p = (int *)mmap(NULL,4,PROT_READ|PROT_WRITE,MAP_SHARED,fd,0);
    close(fd);
    pid = fork();
    if(pid==0){
        *p=2000;
        var=1000;
        printf("child,*p=%d,var=%d\n",*p,var);
    }else{
        sleep(1);
        printf("parent,*p=%d,var=%d\n",*p,var);
    }
}

//2.无血缘关系进程间mmap通信
//*****写端*****
struct student{
    int id;
    char name[256];
    int age;
};
int main(){
    struct student stu={1,"alex",18};
    struct student *p;
    int fd;
    fd = open("test_mmap",O_RDWR|O_CREAT|O_TRUNC,0644);
    ftruncate(fd,sizeof(stu));//修改文件大小
    p = mmap(NULL,sizeof(stu),PROT_READ|PROT_WRITE,MAP_SHARED,fd,0);//创建映射区
    close(fd);//映射区创建后即可关闭文件
    while(1){
        memcpy(p,&stu,sizeof(stu));
        stu.id++;
        sleep(1);
    }
    munmap(p,sizeof(stu));
    return 0;
}
//*****读端*****
struct student{
    int id;
    char name[256];
    int age;
};
int main(){
    struct student stu;
    struct student *p;
    int fd;
    fd = open("test_mmap",O_RDONLY);
    p = mmap(NULL,sizeof(stu),PROT_READ,MAP_SHARED,fd,0);//创建映射区
    close(fd);//映射区创建后即可关闭文件
    while(1){
        printf("id=%d,name=%s,age=%d\n",p->id,p->nane,p->age);
        sleep(1);
    }
    munmap(p,sizeof(stu));
    return 0;
}
//3.匿名映射
```

注意事项：

+ 文件大小
+ read、write权限
+ MAP_SHARED和MAP_PRIVATE
+ 映射区内存不能越界访问



### 信号

> man 7 signal
>
> kill -l
>
> man 2 kill

+ 信号共性：简单、不能携带大量信息、满足条件才发送。

+ 信号特质：信号是软件层面上的“中断”。一旦信号产生，无论程序执行到什么位置，必须立即停止运行，处理信号，处理结束，再继续执行后续指令。所有信号的产生及处理全部都是由【内核】完成的。

+ 产生信号：

  1. 按键产生
  2. 系统调用产生
  3. 软件条件产生
  4. 硬件异常产生
  5. 命令产生

+ 概念：

  + 未决：产生与递达之间状态
  + 递达：产生并且送达到进程。直接被内核处理掉
  + 信号处理方式：执行默认处理动作、忽略、捕捉(自定义)
  + **阻塞信号集**(信号屏蔽字)：本质:位图。用来记录信号的屏蔽状态。一旦被屏蔽的信号，在解除屏蔽前，一直处于未决态。
  + **未决信号集**：本质:位图。用来记录信号的处理状态。该信号集中的信号，表示，已经产生，但尚未被处理。

+ 信号4要素：

  信号使用之前，应先确定其4要素，而后再用！！！

  信号编号、信号名称、信号对应事件、信号默认处理动作

+ 常用信号：

```c++
//kill函数与命令
/*
args：
	pid：
		>0：发送信号给指定进程
		=0：发送信号给跟调用kill函数的那个进程处于同一进程组的进程。
		<-1：取绝对值，发送信号给该绝对值所对应的进程组的所有组员。
		=-1：发送信号给，有权限发送的所有进程。
	signum：信号类型
*/
int kill(pid_t pid,int signum);
```

```c++
//alarm
//每个进程都有且只有唯一一个定时器
//定时发送SIGALRM给当前进程
/*
args：
	seconds：定时秒数
return：
	返回上次定时剩余时间
note：
	time命令：查看程序执行时间。实际时间=用户时间+内核时间+等待时间。优化瓶颈 IO
*/
unsigned int alarm(unsigned int seconds);
```

```c++
int setitimer(int which,const struct itimerval *new_value,struct itimerval *old_value);
int raise(int sig);
void abort(void);
void signal();
```

+ 信号集操作函数

  ![](imgs/signal.png)

  

  ```c++
  /*
  	用自定义信号集去更改阻塞信号集，从而影响未决信号集（操作系统不开放）
  */
  sigset_t set;//自定义信号集
  sigemptyset(sigset_t *set);//清空信号集
  sigfillset(sigset_t *set);//全部置1
  sigaddset(sigset_t *set,int signum);//将一个信号添加到集合中
  sigdelset(sigset_t *set,int signum);//将一个信号从集合中移除
  sigismember(const sigset_t *set,int signum);//判断一个信号是否在集合中
  ```

  ```c++
  //设置信号屏蔽字和解除屏蔽
  /*
  args：
  	how：
  		SIG_BLOCK：设置需要屏蔽的信号	mask = mask|set
  		SIG_UNBLOCK：设置需要解除屏蔽的信号 mask = mask & ~set
  		SIG_SETMASK：替代原始屏蔽集为新屏蔽集 mask = set（不推荐）
  	set：
  	oldset：  
  */
  int sigprocmask(int how,const sigset_t *set,sigset_t *oldset);
  //查看读取未决信号集（操作系统不让修改）
  int sigpending(sigset_t *set);
  ```

  ```c++
  //template
  sigset_t set,oldset,pset;//定义信号集
  sigemptyset(&set);//信号集置零
  sigaddset(&set,SIGINT);//添加信号
  sigaddset(&set,SIGQUIT);
  sigaddset(&set,SIGBUS);
  sigaddset(&set,SIGKILL);
  int ret = sigprocmask(SIG_BLOCK,&set,&oldset);//设置信号屏蔽
  ```

+ **信号捕捉**

  ![](imgs/signal-catch.png)

  特性：
  
  1. 信号捕捉函数工作期间，取 sa_mask 和默认信号屏蔽字的并集来指定信号屏蔽字，信号处理函数结束后，才恢复默认的信号屏蔽字
  2. XXX信号捕捉函数执行期间，该信号自动被屏蔽（sa_flags=0时生效）
  3. 阻塞的常规信号不支持排队，产生多次只记录一次（后32实时信号支持排队）
  
  ```c++
  //信号捕捉函数
  // typedef void (*sighandler_t)(int)
  signal(int signum,sighandler_t handler);//简单，不同系统可能不一样
  sigaction(int signum,const struct sigaction *act,struct sigaction *oldact); //通用、重点
  
  // sigaction 结构体
  struct sigaction{
      void (*sa_handler)(int);//handler函数指针
      void (*sa_sigaction)(int,siginfo_t *,void *);//一般不用
      sigset_t sa_mask;//设置临时信号屏蔽字，通常传0
      int sa_flags;	//通常传0
      void (*sa_restorer)(void);//废弃
  }
  ```
  
  ```c++
  //template -- signal
  void sig_catch(int signum){
      cout<<"catch you!! "<<signum<<endl;
      return;
  }
  signal(SIGINT,sig_catch);
  ```
  
  ``` c
  //template -- sigaction
  void sig_catch(int signum){
  	cout<<"catch signal "<<signum<<" !"<<endl;
  	return;
  }
  int main(){
  	struct sigaction act,oldact;
  
  	act.sa_handler = sig_catch;			//设置回调函数
  	sigemptyset(&act.sa_mask);			//设置信号屏蔽字，只在sig_catch工作期间生效
  	act.sa_flags = 0;					//默认传0
  
  	int ret = sigaction(SIGINT,&act,&oldact);//注册信号捕捉函数
  	if(ret == -1){
  		cout<<"sigaction error"<<endl;
  	}
  	while(1);
  	return 0;
  }
  ```
  
  
  
  ```c++
  //template
  //借助信号捕捉回收子进程 P140
  void sig_catch(int signum){
  	pid_t wpid;
  	int status;
  	while((wpid = waitpid(-1,&status,0))!=-1){
  		if(WIFEXITED(status))
  			cout<<"-------------------wait for child with pid "<<wpid<<" & return "<<WEXITSTATUS(status)<<endl;
  	}
  }
  int main(){
  	pid_t pid;
  	int i;
  	//阻塞信号
  	sigset_t set;
  	sigemptyset(&set);
  	sigaddset(&set,SIGCHLD);
  	sigprocmask(SIG_BLOCK,&set,NULL);
      
  	for(i=0;i<15;i++){
  		if((pid = fork()) == 0)
  			break;
  	}
  	if(i == 15){//主进程
  		struct sigaction act;
  		act.sa_handler = sig_catch;
  		sigemptyset(&act.sa_mask);
  		act.sa_flags = 0;
  
  		//注册信号捕捉函数
  		sigaction(SIGCHLD,&act,NULL);
  		//解除信号阻塞
  		sigprocmask(SIG_UNBLOCK,&set,NULL);
  		cout<<"I'm parent with pid "<<getpid()<<endl;
  	}else{//子进程
  		cout<<"I'm "<<i+1<<"th child with pid "<<getpid()<<endl;
  		return i;
  	}
  	return 0;
  }
  ```
  



## 进程组和会话

```c++
//获取进程所属的会话ID
pid_t getsid(pid_t pid); 
//创建一个会话，并以自己的ID设置进程组ID，同时也是新会话的ID
pid_t setsid(void); 
```



## 守护进程

> 守护进程用脚本管理

daemon进程。通常运行于操作系统后台，脱离控制终端。一般不与用户直接交互。周期性的等待某个事件发生或周期性执行某一动作。
不受用户登录注销影响。通常采用以d结尾的命名方式。如 sshd、httpd 等

### 创建步骤

1. fork子进程，让父进程终止。
2. 子进程调用 setsid() 创建新会话
3. 通常根据需要，改变工作目录位置 chdir()
4. 通常根据需要，重设umask文件权限掩码
5. 通常根据需要，关闭/重定向 文件描述符（将0、1、2重定向到/dev/null）
6. 守护进程 业务逻辑。while ()

```c++
//template
pid_t pid;	

pid = fork();
if(pid>0)		//父进程终止
    exit(0);

if(setsid() == -1)	//创建新会话
    sys_err("setsid error");

chdir("/root/study");	//改变工作目录
umask(0022);			//改变文件访问权限掩码

close(STDIN_FILENO);	//关闭文件描述符0
int fd = open("/dev/null",O_RDWR);	// fd-->0
if(fd==-1)
    sys_err("open file error");

dup2(fd,STDOUT_FILENO);		//重定向 stdout & stderr
dup2(fd,STDERR_FILENO);

while (1);		//模拟守护进程业务
return 0;
```



# 线程

> 轻量级进程（light-weight process，LWP）
>
> 线程中直接返回 errno，不用 perror

```sh
ps -Lf 进程id
# 线程号 LWP
```

进程：资源分配的基本单位，有独立的 PCB 和进程地址空间

线程：CPU调度的基本单位（最小的执行单位），有独立的PCB，没有独立的进程地址空间

```c++
//注：线程中检查出错信息
fprintf(stderr,"xxx error：%s\n",strerror(ret));
//使用线程需注意编译时带上 -lpthread
//link with -pthread
g++ hello.cpp -o hello -lpthread
```



## template

```c
//thread template
struct student{
    int no;
    char name[32];
};
void *callback(void *arg){
    //法1	两处法1配套使用
    struct student *stu;
    stu = malloc(sizeof(stu));
    //法2	两处法2配套使用
    //struct student *stu = (struct student *)arg;
    stu->no = 666;
    strcpy(stu->name,"alex");
    return (void*)stu;
}
int main(){
    pthread_t tid;
    struct student *rstu,stu;
    //法1
    int ret = pthread_create(&tid,NULL,callback,NULL);
    //法2
    //int ret = pthread_create(&tid,NULL,callback,(void*)&stu);
    ret = pthread_join(tid,(void**)&rstu);
    cout<<"child thread return with no="<<rstu->no<<", name="<<rstu->name<<endl;
    pthread_exit(NULL);
}


```

```c
//简洁版
void *callback(void *arg){
	int num = 66;
	return (void *)num;
}
int main(){
	pthread_t tid;
	int ret;

	pthread_create(&tid,NULL,*callback,NULL);
	pthread_join(tid,(void **)&ret);
	cout<<"ret = "<<ret<<endl;
	return 0;
}
//复杂版
struct student{
	int no;
	char name[32];
};
void *callback(void *arg){
	struct student *stu;
	stu = new struct student;

	stu->no = 1;
	strcpy(stu->name,"alex");
	return (void *)stu;
}
int main(){
	pthread_t tid;
	int ret;
	struct student *rstu;

	pthread_create(&tid,NULL,*callback,NULL);
	pthread_join(tid,(void **)&rstu);
	cout<<"ret = "<<rstu->no<<" "<<rstu->name<<endl;
	delete rstu;
	return 0;
}
```





## 三级映射



## 线程资源

### 共享

1. 文件描述符表
2. 每种信号的处理方式
3. 当前工作目录
4. 用户ID和组ID
5. 内存地址空间（.text/.data/.bss/heap/共享库）
6. **全局变量**

### 不共享

1. 线程id
2. 处理器现场（寄存器）和栈指针（内核栈）
3. 独立的**栈空间**（用户空间栈）
4. errno 变量
5. 信号屏蔽字
6. 调度优先级



## 优缺点

优点：

1. 提高程序并发性
2. 开销小
3. 数据通信、共享数据方便

缺点：

1. 库函数，不稳定 
2. 调试、编写困难、gdb 不支持
3. 对信号支持不好

优点相对突出，缺点均不是硬伤。Linux 下由于实现方法导致进程、线程差别不是很大。

优先选择线程，简单，性能高



## func

### pthread_self

```c++
//返回当前线程号
pthread_t pthread_self(void);
```

### pthread_create

```c++
/*
args:
	tid：返回新创建的子线程id
	attr：线程属性，传NULL使用默认属性
	start_rountn：子线程回调函数。     void* fun(void*){}
	arg：回调函数的参数。没有填NULL
return：
	success：0
	fail：errno
*/
int pthread_create(pthread_t *tid,const pthread_attr_t *attr,void *(*start_rountn)(void*),void *arg);
```

### pthread_exit

```c++
//exit(0) 直接退出进程
//return NULL 表示返回到调用者
void pthread_exit(void *retval);//退出当前线程
```

### pthread_join

```c++
//阻塞回收线程
int pthread_join(pthread_t thread,void **retval);

//template
struct student{
    int no;
    char name[32];
};
void *callback(void *arg){
    //法1
    struct student *stu;
    stu = malloc(sizeof(stu));
    //法2
    //struct student *stu = (struct student *)arg;
    stu->no = 666;
    strcpy(stu->name,"alex");
    return (void*)stu;
}
int main(){
    pthread_t tid;
    struct student *rstu,stu;
    //法1
    int ret = pthread_create(&tid,NULL,callback,NULL);
    //法2
    //int ret = pthread_create(&tid,NULL,callback,(void*)&stu);
    ret = pthread_join(tid,(void**)&rstu);
    cout<<"child thread return with no="<<rstu->no<<", name="<<srtu->name<<endl;
    pthread_exit(NULL);
}

//practice
//使用 pthread_join 函数将循环创建的多个子线程回收
```

### pthread_detach

线程分离状态：指定该状态，线程主动与主控线程断开关系。线程结束后，其退出状态不由其他线程获取，而直接自己自动释放。

网络、多线程服务器常用

```c++
//实现线程分离
//还可通过线程属性来设置游离态
int pthread_detach(pthread_t thread);
```

### pthread_cancel

```c++
//杀死一个线程，需要到达取消点(保存点)
int pthread_cancel(pthread_t thread);
/*
	如果，子线程没有到达取消点， 那么pthread_cancel无效。
	我们可以在程序中，手动添加一个取消点。使用 pthread_testcancel()
	成功被 pthread_cancel() 杀死的线程，返回-1.使用pthread_join 回收。
*/
```



## 线程属性

```c++
//通过线程属性设置游离态
pthread_attr_t attr;										 //创建一个线程属性结构体变量
pthread_attr_init(&attr);									 //初始化线程属性
pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_DETACHED);	//设置线程属性为分离态
pthread_create(&tid,&attr,callback,NULL);					  //借助修改后的 设置线程属性 创建为分离态的新线程
pthread_attr_destory(&attr);							     //销毁线程属性
```



## 注意事项

1. 主线程退出其他线程不退出，主线程应调用 pthread_exit

2. 避免僵尸线程

   + pthread_join
   + pthread_detach
   + pthread_create 指定分离属性

   被 join 线程可能在 join 函数返回前就释放完自己的所有内存资源，所以不应当返回被回收线程栈中的值

3. malloc 和 mmap 申请的内存可以被其他线程释放

4. 应避免在多线程模型中调用 fork 除非，马上 exec，子进程中只有调用 fork 的线程存在，其他线程在子进程中均 pthread_exit 

5. 信号的复杂语义很难和多线程共存，应避免在多线程引入信号机制



## 线程同步

线程同步：协同步调，对公共区域数据按序访问。防止数据混乱，产生与时间有关的错误。

### 锁

锁：建议锁，对公共数据进行保护。所有线程 **应该** 在访问公共数据前先拿锁再访问。但，锁本身不具备强制性。

### 互斥锁

mutex（互斥量、互斥锁）使用步骤：

1. 创建锁
2. 初始化
3. 加锁
4. 访问共享数据（stdout）
5. 解锁
6. 销毁锁

```c++
pthread_mutex_t lock;
pthread_mutex_init;		//1
pthread_mutex_lock;		//1--  -->  0
pthread_mutex_unlock();	//0++  -->  1
pthread_mutex_destroy();
```

注意事项：

+ 尽量保证锁的粒度， 越小越好。（访问共享数据前，加锁。访问结束 **立即** 解锁）
+ 互斥锁，本质是结构体。我们可以看成整数。 初值为 1。（pthread_mutex_init()函数调用成功）
+ 加锁：--操作
+ 解锁：++操作
+ try锁：尝试加锁，成功--。失败，返回。同时设置错误号 EBUSY

restrict 关键字：用来限定指针变量。被该关键字限定的指针变量所指向的内存操作，必须由本指针完成。



### 死锁

1. 对一个锁反复 lock

   ![](imgs/lock1.png)

2. 两个线程，各自持有一把锁，请求另一把

   ![](imgs/lock2.png)



### 读写锁

与互斥量类似，但读写锁允许更高的并行性。其特性为：**写独占，读共享**

**读锁**：以读方式给数据加锁

**写锁**：以写方式给数据加锁

+   锁只有一把
+ 读共享，写独占
+ 写锁优先级高

```c++
//定义读写锁变量
pthread_rwlock_t rwlock;
//读写锁操作函数
pthread_rwlock_init();
pthread_rwlock_rdlock();
pthread_rwlock_wrlock();
pthread_rwlock_unlock();
pthread_rwlock_destroy();

pthread_rwlock_tryrdlock();
pthread_rwlock_trywrlock();

```



### 条件变量 

本身不是锁，但通常结合锁（mutex）来使用

```c++
pthread_cond_t cond;
//初始化条件变量
pthread_cond_init(&cond,NULL);					//动态初始化
pthread_cond_t cond = PTHREAD_COND_INITIALIZER;	  //静态初始化

int pthread_cond_init();
int pthread_cond_wait();
int pthread_cond_timedwait();
int pthread_cond_destroy();
int pthread_cond_signal();
int pthread_cond_broadcast();
```

```c++
//template
//条件变量实现生产者消费者模型
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t cond = PTHREAD_COND_INITIALIZER;
struct msg{
	int num;
	struct msg *next;
};
struct msg *head = NULL;

void terror(int ret,const char *str){
	if(ret!=0){
		cerr<<str<<"："<<strerror(ret)<<endl;
		pthread_exit(NULL);
	}
}
void *producer(void *arg){
	while (1)
	{
		struct msg *m = (struct msg*)malloc(sizeof(struct msg));

		pthread_mutex_lock(&mutex);
		m->next = head;
		m->num = rand()%1000+1;
		head = m;
		cout<<"*****producer make "<<m->num<<endl;
		pthread_mutex_unlock(&mutex);
		pthread_cond_signal(&cond);

		sleep(rand()%3);
	}
}
void *consumer(void *arg){
	while (1)
	{
		pthread_mutex_lock(&mutex);
		while(head == NULL){
			pthread_cond_wait(&cond,&mutex);//1.阻塞等待条件变量，解锁。2.返回时，重写加锁
		}
		//模拟消费
		struct msg *m;
		m = head;
		head = m->next;
		cout<<"=====consumer use "<<m->num<<endl;
		pthread_mutex_unlock(&mutex);

		free(m);
		sleep(rand()%3);
	}	
}
int main(){
	pthread_t pid,cid;
	srand(time(NULL));

	int ret = pthread_create(&pid,NULL,producer,NULL);
	terror(ret,"pthread_create error");
	ret = pthread_create(&cid,NULL,consumer,NULL);
	terror(ret,"pthread_create error");

	pthread_join(pid,NULL);
	pthread_join(cid,NULL);

	return 0;
}
```



### 信号量

```c++
sem_t sem;
int sem_init();
int sem_destroy();
int sem_wait();
int sem_trywait();
int sem_timedwait();
int sem_post();
```



# 进程线程对比

| 线程             | 进程               |
| ---------------- | ------------------ |
| pthread_create() | fork()             |
| pthread_self()   | getpid()           |
| pthread_exit()   | exit()             |
| pthread_join()   | wait() / waitpid() |
| pthread_cancel() | kill()             |
| pthread_detach() | 无对应，可创建会话 |





 
