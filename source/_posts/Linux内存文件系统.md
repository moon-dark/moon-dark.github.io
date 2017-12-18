---
title: Linux内存文件系统
date: 2017-12-18 09:34:22
tags:
  - code
  - Linux
---
# Linux内存文件系统

## 一、RamDisk
### RamDisk的使用

RamDisk有三种实现方式：
第一种就是传统意义上的，可以格式化，然后加载。 这在Linux内核2.0/2.2就已经支持，其不足之处是大小固定，之后不能改变。为了能够使用Ramdisk，我们在编译内核时须将block device中的Ramdisk支持选上，它下面还有两个选项，一个是设定Ramdisk的大小，默认是4096k；另一个是initrd的支持。
如果对Ramdisk的支持已经编译进内核，我们就可以使用它了。

查看可用的RamDisk
```Bash
# ll /dev/ram*
lrwxrwxrwx  1 root root     4 Feb 20 00:49 /dev/ram -> ram1
brw-rw----  1 root disk 1,  0 Apr 27 11:44 /dev/ram0
brw-rw----  1 root disk 1,  1 Feb 20 00:49 /dev/ram1
```
创建一个目录
```Bash
# mkdir /mnt/testRamDisk
```
对/dev/ram0 创建文件系统
```Bash
# mke2fs /dev/ram0
```

挂载/dev/ram0
```Bash
# mount /dev/ram0 /mnt/testRamDisk      /dev/ram0
# df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/ram0              16M  140K   15M   1% /mnt/testRamDisk
```

然后就可以象对普通硬盘一样对它进行操作了。
另两种则是内核2.4才支持的，通过Ramfs或者Tmpfs来实现：
它们不需经过格式化，用起来灵活，其大小随所需要的空间而增加或减少。

## 二、ramfs
Ramfs顾名思义是内存文件系统，它它处于虚拟文件系统（VFS）层，而不像ramdisk那样基于虚拟在内存中的其他文件系统(ex2fs)。 因而，它无需格式化，可以创建多个，只要内存足够，在创建时可以指定其最大能使用的内存大小。
### Ramfs的使用
如果你的Linux已经将Ramfs编译进内核，你就可以很容易地使用Ramfs了。创建一个目录，加载Ramfs到该目录即可：
```Bash
# mkdir  /testRam
# mount -t ramfs none /testRam
# df -aH #可以看到已经挂载了testRam
Filesystem             Size   Used  Avail Use% Mounted on
none                      0      0      0   -  /testRam
```
缺省情况下，Ramfs被限制最多可使用内存大小的一半。可以通过maxsize（以kbyte为单位）选项来改变。
```# mount -t ramfs none /testRAM -o maxsize=2000 (创建了一个限定最大使用内存为2M的ramdisk）
```

## 三、tmpfs
tmpfs是一种基于内存的文件系统。
### tmpfs和VM
tmpfs可以使用物理内存，也可以使用交换分区。在Linux内核中，虚拟内存资源由物理内存（RAM）和交换分区（Swap）组成，这些资源是由内核中的虚拟内存子系统来负责分配和管理。
传统的ramdisk是个块设备，要用mkfs来格式化它，才能真正地使用它；而tmpfs是一个文件系统，并不是块设备，只是安装它，就可以使用了。
tmpfs向虚拟内存子系统请求页来存储文件，它同Linux的其它请求页的部分一样，不知道分配给自己的页是在内存中还是在交换分区中。同Ramfs一样，其大小也不是固定的，而是随着所需要的空间而动态的增减。
### tmpfs的使用
#### 关于/dev/shm
使用tmpfs，首先你编译内核时得选择”虚拟内存文件系统支持（Virtual memory filesystem support）。
默认的Linux发行版中的内核配置都会开启tmpfs，映射到了/dev/下的shm目录。可以通过df 命令查看：
```Bash
$ df -aT
Filesystem    Type   1K-blocks      Used Available Use% Mounted on
tmpfs                   17G    11M    17G   1% /dev/shm
```
/dev/shm/是linux下一个非常有用的目录，因为这个目录不在硬盘上，而是在内存里。因此在linux下，就不需要大费周折去建ramdisk，直接使用/dev/shm/就可达到很好的优化效果。该目录的文件系统是tmpfs的，因此这个目录下的文件访问非常快。
/dev/shm默认为一半内存大小。在某些场合可能不够用，并且默认的inode数量很低一般都要调高些，这时可以用mount命令来管理它。
```#mount -o size=1500M -o nr_inodes=1000000 -o noatime,nodiratime -o remount /dev/shm
```
如果需要永久修改/dev/shm的值，需要修改/etc/fstab
```tmpfs /dev/shm tmpfs defaults,size=1.5G 0 0
mount -o remount /dev/shm
```
#### /dev/shm应用
首先在/dev/shm建个tmp文件夹，然后与实际/tmp绑定
```Bash
# mkdir /dev/shm/tmp
# chmod 1777 /dev/shm/tmp
# mount –bind /dev/shm/tmp /tmp（–bind ）
```
在使用mount –bind olderdir newerdir命令来挂载一个目录到另一个目录后，newerdir的权限和所有者等所有信息会发生变化。挂载后的目录继承了被挂载目录的所有属性，除了名称。
### 创建tmpfs
tmpfs并不是存在于一个底层块设备上面。因为tmpfs是直接建立在VM之上的，用一个简单的mount命令就可以创建tmpfs文件系统了。
```
# mount tmpfs /mnt/tmpfs -t tmpfs
# df -h
Filesystem            Size  Used Avail Use% Mounted on
tmpfs                  24G     0   24G   0% /mnt/tmpfs
```
为了防止tmpfs使用过多的内存资源而造成系统的性能下降或死机，可以在加载时指定tmpfs文件系统大小的最大限制。
```# mount tmpfs /mnt/tmpfs -t tmpfs -o size=32m
```

## 注意
ramfs只会在物理内存中被创建，而tmpfs可能在物理内存中创建，也可能在交换分区中被创建。对于想利用内存的高速IO来提高效能的应用，最好是使用ramfs。对于只是想存放临时缓存的应用，最好是用tmpfs，以提前内存的利用率。
但是，对于高负载的服务器，使用内存文件系统实际上得不偿失，在系统内存很快用光的情况下，操作系统将进行磁盘交换，导致系统性能严重下降，这比起直接使用磁盘文件系统更为糟糕。事实上，在高负载的服务器环境下，允许系统使用更多的内存来缓冲磁盘数据，更有效的发挥系统的磁盘缓冲能力，在实际使用中更为有效。
