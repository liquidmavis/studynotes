# 服务器

## 计算机概论

内存主要组件为动态随机存取内存(DRAM),断电之后就会丢失数据

当前发展为SDRAM和DDR SDRAM，DDR全名为双倍传输速度

很多内存条都要DDR3或DDR4字样



内存的多通道设计：因为所有数据都需要存储在内存，内存的数据位宽越大越好，但受限于传统总线位宽为32和64位，为了加大位宽，把两个或多个内存条集合在一起存储数据，达到双通道和多通道的设计，一般内存条需要保持相同的频率型号 



## 文件系统

### EXT2

linux的文件系统成为ext2(Linux Second Extended file system)

由超级区块、inode和数据块组成



数据块：

- 原则上格式化后，固定大小
- 每个块只能放一个文件的信息
- 如果装满了就需要额外的数据块
- 如果没有装满，剩余空间也不能用了



inode：

- 保存文件属性和权限
- 一个inode对应一个文件
- inode记录了数据块的位置



超级区块：

- 数据块与inode总量
- 未使用与使用的数据块和inode总量
- 数据区块与inode大小



#### 操作

当写入文件时候

1. 先判断用户有没有wr权限
2. 查找超级区块的inode对照表找到没有使用的inode号码，写入属性和权限
3. 根据区块对照表分配区块，并与inode相连接
4. 将inode与区块数据同步到inode对照表和区块对照表，更新超级区块内容



#### 链接

| 硬链接 | ln命令  在目录下新增一条记录链接到指定inode中  不占空间、不能跨文件系统、不能链接目录 |
| ------ | ------------------------------------------------------------ |
| 软连接 | ln -s命令 占用实际空间，有自己独立的inode和数据块            |





## 命令

### 重要热键

SHIFT+PAGE UP/PAGE DOWN 用来翻页



### help

cat --help

--help只能用在你使用过的命令上，不能用在从来没有使用的命令上

```linux
[root@liquid dev]# cat --help
Usage: cat [OPTION]... [FILE]...
Concatenate FILE(s), or standard input, to standard output.

  -A, --show-all           equivalent to -vET
  -b, --number-nonblank    number nonempty output lines, overrides -n
  -e                       equivalent to -vE
  -E, --show-ends          display $ at end of each line
  -n, --number             number all output lines
  -s, --squeeze-blank      suppress repeated empty output lines
  -t                       equivalent to -vT
  -T, --show-tabs          display TAB characters as ^I
  -u                       (ignored)
  -v, --show-nonprinting   use ^ and M- notation, except for LFD and TAB
      --help     display this help and exit
      --version  output version information and exit

With no FILE, or when FILE is -, read standard input.

Examples:
  cat f - g  Output f's contents, then standard input, then g's contents.
  cat        Copy standard input to standard output.

```



### man

如果没有见过某个命令可以使用man

man是manual操作说明的简写，执行

man date就会出现清楚说明如下

```
NAME
       date - print or set the system date and time

SYNOPSIS
       date [OPTION]... [+FORMAT]
       date [-u|--utc|--universal] [MMDDhhmm[[CC]YY][.ss]]

DESCRIPTION
       Display the current time in the given FORMAT, or set the system date.


```

在该界面下，用/string可以查询文档中string这个相关字符



### 目录处理

cd 切换目录

pwd 显示当前目录

mkdir 创建目录

rmdir 删除目录



### 环境变量

为什么在任意位置输入ls查询都不会报错，该命令可是在/bin/ls中啊，那是因为系统配置了环境变量PATH

当输入某个命令时，系统会去查找每个PATH定义的路径下的可执行文件

通过echo $PATH可以输出当前涵盖的路径

```unix
[root@liquid ~]# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
```

如果要添加某个路径下的命令到PATH中可以用一下命令

```
[root@liquid ~]# PATH="${PATH}:/某个路径"
```



### 复制移动与删除

cp:复制命令

​	-a 相当于-dr --perseve=all的意思

​	-i 若目标文件已存在，则覆盖前先询问

​	-r 递归复制

​	-p 连同文件属性一起复制过去

rm:删除命令

mv：移动文件命令



### 文件格式

df：列出文件系统整体磁盘用量

du：查看文件系统的磁盘使用量



### 获取文件的文件名和目录名称

```
#文件的目录名称
[root@liquid heelo]# dirname /root/heelo/fie 
/root/heelo
#文件的名称
[root@liquid heelo]# basename /root/heelo/fie
fie
```



### 文件内容查看

cat:输出文件内容

more：一页一页显示内容

less：更高级

head：只看前几行

tail：只看后几行

​	tail -f 文件名 持续检测文件内容，在tomcat查看日志时候经常用到



### 文件查找

whereis:针对/bin/sbin下面执行文件 /user/share/man等几个特殊目录

locate：查找/var/lib/mlocate下数据库存好的文件，当找不到文件时候可以用updatedb更新数据库

​	上面两个命令都比find快，一个只查找几个目录，一个有专门的数据库查找

find：全目录检索，速度要比上面的慢



### 账户管理

linux下每个用户都有UID和GID存储在/etc/passwd中，里面把数字和用户名一一对应



#### 用户组

linux文件都具有用户、用户组和其他人三种身份的权限

默认情况下，linux的root等账户相关信息记录在/etc/passwd中

​						linux的所有组名放在/etc/group中

​						个人密码放在/etc/shadow中



```
rwxr-xr-x
r读、w写、x执行
代表文件拥有这有rwx权限
文件所属组有r-x
其他人-x
```



##### useradd

添加用户

```
[root@liquid /]# useradd -u 1500 -g project mavis
[root@liquid /]# id mavis
uid=1500(mavis) gid=1003(project) groups=1003(project)
以1500uid，创建名字为mavis的用户，归属project组
```



##### usermode

主要对用户进行用户组的修改

```
[root@liquid /]# usermod -a -G project alex
alex加入到project组中
```



##### 新增和删除用户组

groupadd

groupmod

groupdel



##### chgrp

修改文件所属用户组

##### chown

修改文件拥有者

##### chmod

修改文件的权限







