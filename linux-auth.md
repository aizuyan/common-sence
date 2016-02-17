## Linux中的文件权限
对于Linux中的文件（Linux中一切皆文件）权限，我只了解到了用户、用户组、其他用户对文件的读写执行（wrx），对于文件类型了解不是很全面，
对于SUID、SGID的了解也不是很全面，今天查阅资料总结记录下。

#### 文件类型
```shell
[www@localhost laravel]$ ll
drwxrwxr-x. 10 www www 4096 12月 19 00:39 app
-rwxrwxr-x.  1 www www 1646 12月 19 00:39 artisan
```
当在一个目录下面使用ll的时候可以看到类似上面的内容，第一列表示了文件的所有权限信息，第一位表示文件类型，后面9位是三组（wrx）。

文件类型在linux中有下面几种类型：

p 表示管道文件
	管道分为两种——匿名管道和命名管道，这里看到的是命名管道
	匿名管道——父子进程之间传输数据的一种没有名字的管道，匿名管道是半双工的，只能单向通讯，只能在本地计算机中使用。
	命名管道——以文件名的形式存在，却有着和匿名管道类似的行为
	eg:
	创建命名管道：
```c
//创建命名管道文件
void main()
{
	mkfifo("/tmp/pipetest", "rw");
}
```
运行之后执行`ll /tmp`，会看到下面内容
[www@localhost ~]$ ll /tmp/
prwx-ws---. 1 www  www      0 12月 20 10:30 pipetest
```c
//创建进程读命名管道
#include <stdio.h>
#define BUFFER_LEN 10
int main()
{
	FILE *in_file;
	char buf[BUFFER_LEN];
	in_file = fopen("/tmp/pipetest", "r");
	if(in_file == NULL)
	{
		printf("Error fopen!!");
		return 1;
	}
	while(fread(buf, 1, BUFFER_LEN, in_file) > 0)
	{
		printf("get from pipe %s\n", buf);
		fclose(in_file);
	}
	return 0;
}
//创建进程写管道命令
#include <stdio.h>
#define BUFFER_SIZE 100
int main()
{
	FILE *out_file;
	char buf[BUFFER_SIZE];
	out_file = fopen("/tmp/pipetest", "w");
	if(out_file == NULL)
	{
		printf("Error Fopen!!");
		return 1;
	}
	sprintf(buf, "This is a good test of C piipe!!!");
	fwrite(buf, 1, BUFFER_SIZE, out_file);
	fclose(out_file);
	return 0;
}
```
创建完成之后编译运行，发现读进程会一直阻塞知道有输入为止，管道可以被多个进程打开，数据输出到最新打开的进程。

d 目录文件
l 符号链接文件
- 普通文件
s socket文件
	[www@localhost ~]$  ll /usr/local/mysql/mysql.sock
	srwxrwxrwx. 1 mysql mysql 0 12月 16 09:29 /usr/local/mysql/mysql.sock
c 字符设备文件
b 块设备文件

## sticky-bit
对一个文件设置了sticky-bit之后，尽管其他用户有写权限， 也必须由属主执行删除、移动等操作。

对一个目录设置了sticky-bit之后，存放在该目录的文件仅准许其属主执行删除、 移动等操作

## SUID和SGID
SUID和SGID是在执行程序时候起作用。

SUID优先级比SGID高。

SUID设置文件，SGID设置目录。

Linux中会给每个用户生成4个id：uid、gid、euid、egid，内核主要根据euid、egid来确定进程对资源的访问权限，一个进程如果没有SUID或SGID位，则
euid=uid，egid=gid，分别是当前用户的uid和gid
举个例子：
	jhon用户uid=201,gid=202
	rito用户uid=203,gid=204
rito去访问jhon的一可执行个文件（未设置SUID、GUID），此时内核会设置euid=203，egid=204，发现会没有权限
如果jhon设置了SUID位，则euid=201，egid=202，运行的时候会和jhon自己运行一样。

看个/bin/ping的例子：
```sh
[www@localhost ~]$ ll /bin/ping
-rwsr-xr-x. 1 root root 38200 7月  24 01:55 /bin/ping
[root@localhost ~]# chmod u-s /bin/ping
[root@localhost ~]# su www
[www@localhost root]$ ping baidu.com
ping: icmp open socket: 不允许的操作
[www@localhost root]$ exit
exit
[root@localhost ~]# chmod u+s /bin/ping
[root@localhost ~]# su www
[www@localhost root]$ ping baidu.com
PING baidu.com (220.181.57.217) 56(84) bytes of data.
64 bytes from 220.181.57.217: icmp_seq=1 ttl=128 time=8.21 ms
64 bytes from 220.181.57.217: icmp_seq=2 ttl=128 time=7.70 ms
```
上面的例子中，经常使用的ping文件就设置了SUID，当我们运行ping的时候会具有root相关的权限

## 参考资料
[http://www.cnblogs.com/fhefh/archive/2011/09/20/2182155.html](http://www.cnblogs.com/fhefh/archive/2011/09/20/2182155.html)
