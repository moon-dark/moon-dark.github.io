---
title: 读《Android安全架构深究》：用户管理
date: 2017-02-23 18:25:48
tags:
  - Android
  - 用户
  - code
---

**用户类型：**
> **主用户**（持有者）：第一个用户，默认创建，一直存在。userID是0，具有所有权限，可以创建、删除其他用户，更改影响所有用户的设置。
> **次要用户**：除受限用户所有后添加的用户。
> **受限用户**：受限用户基于主用户，以特定限制条件共享它的应用、数据和账号。主用户控制哪个应用对手向用户可用。
> **访客用户**：支持一个访客用户，默认禁用。具体用途不明。
<!--more-->
**用户管理：**
命令行工具：

```Bash
 pm list users
 pm get-max-users
 pm create-user hamilton
 pm remove-user USER_ID
 am switch-user User_ID #To switch between users[/shell]
```

用户状态和相关广播：
> UserManagerService服务发送一些广播，通知其他用户事件相关的组件。
> USER_ADDED
> USER_REMOVED
> USER_INFO_CHANGED
> 切换用户时触发：USER_BACKGROUND、USER_FOREGROUND和USER_SWITCH
> Android最大支持8个用户，但只能同时运行3个用户。
> 用户被关闭：USER_STARTING、USER_STARTED、USER_STOPPING、USER_STOPPED
**用户元数据：**
> 用户数据存放在/data/system/users目录下
> flags决定用户类型：
> FLAG_PRIMARY（0x00000001）标记为主用户
> FLAG_ADMIN （0x00000002）标记为管理员用户，可创建、删除用户。
> FLAG_GUSET （0x00000004）标记为访客用户
> FLAG_RESTRICTED（0x00000008）标记为受限用户
> FLAG_INITIALIZED（0x00000010）标记用户完全初始化
> 实际中主用户的属性是19（0x13，FLAG_INITIALIZED|FLAG_ADMIN|FLAG_PRIMARY ）
> 次要用户的属性是16（0x10，FLAG_INITIALIZED）
> 受限用户的属性是24（0x18，FLAG_INITIALIZED|FLAG_RESTRICTED）
> 每个用户系统目录都包含用户特定的系统设置和数据,但是没有应用程序的数据.
**每个用户的应用程序管理：**
> **应用程序数据目录**
> 主用户的应用程序数据任就存放在/data/data目录下（向后兼容）；
> 用户数据目录存储在/data/user/目录下，以用户ID命名。设备拥有者目录（0/）是一个指向/data/data目录的符号链接。
> 在多用户设备上，app的UID来自于用户ID和app ID：uid = userId * 100000 + (appId % 100000)。
> **应用共享**
> 每个用户都有一个专用的数据目录，APK文件时所有用户共享的：
> APK文件复制到/data/app，对所有用户是可读；
> 共享库复制到/data/app-lib/&lt;包名&gt;/目录下，符号链接到/data/user/&lt;用户ID&gt;/&lt;包名称&gt;/lib/
> 应用优化过的DEX文件，存放在/data/dalvik-cache/目录下，同样是所有用户共享的。
>
> Android通过在每个用户的系统目录下创建package-restrictions.xml文件，实现使得每个用户拥有不同的应用程序：
> 记录某个应用是否启用；
> 包的安装状态；
> 每个应用禁用的组件信息；
> 处理可被多个应用处理的intent时，首选启动的应用程序列表。
>
> 当设备用户安装一个应用，一个inst="false"的标签被添加到所有用户的package-restrictions.xml文件；
> 当另一个用户安装相同的应用，inst属性移除，认为该应用对这个用户是已经安装的；
> 受限用户不能安装应用程序，但是设备拥有者为受限用户启用应用时，执行流程是一样的。
**外存：**外存是一个对大小写不敏感的、具有不变的POSIX权限类和模式的文件系统。
> **外存实现**
> 从Android 4.4开始，APP可以在外存中管理它们自己的目录（Android/data/com.example.com/），而不需要请求       WRITE_EXTERNAL_STORAGE权限，这个权限用于授权对所有外存数据的访问，包括相机照片、视频、其他多媒体资源。
> **多用户外存**
> Android CDD需求：Android设备上的每个用户必须有各自的、互相隔离的外存目录。
> 使用三个Linux内核特性：挂载命名空间、绑定挂载、共享子树。
> 挂载命名空间：允许每个进程拥有自己的挂载点，因此可以使用与其他进程不同的目录结构。当前每个进程的挂载点，通过/proc/PID/mounts文件读取。
> 绑定挂载：允许一个目录/文件挂载到目录数的其他路径下，使得在多个不同位置可以看到相同的目录或文件。
> 共享子树：提供一种方式，来控制文件系统的挂载点如何在命名空间中传播。共享子树使如下事情成为可能：进程拥有自己的命名空间，但仍旧可以访问进程启动之后挂载的文件系统。
> 共享子树提供四种不同的挂在类型，Android使用其中的共享挂载和从属挂载：父级命名空间创建的共享挂载，会传播到所有子级命名空间；一个从属挂载有一个共享挂载的主挂载点，同样传播新的挂载点。
> 传播只有一种方式：主挂载点向其从属挂载点传播，从属挂载点不会向主挂载点传播。
**        Android上的实现：**
> 在主外存依赖内部存储的设备上，sdcard FUSE守护进程使用/data/media/目录作为源，并且在/mnt/shell/emulated创建了一个模拟的文件系统。
> 每个用户有专用的外存数据目录，并以其用户ID命名。外存设置由两步实现：首先所有共享的挂载点在zygote进程中设置，然后对各自进程可见的专有挂载点，作为每个进程的一部分设置。
> 代码部分：dalvik/vm/Init.cpp，函数initZygote；dalvik/vm/native/dalvik_system_Zygote.cpp，函数mountEmulatedStorage。从unshare创建子进行开始的方法。
**外存权限**
> 模拟伪存上的FAT文件系统，sdcard FUSE守护进程指派固定的用户、组、访问权限到外存上的每个目录与文件。权限不可更改，均不支持符号链接与软链接。指派的拥有者和权限，由sdcard守护进程使用的权限派生模式决定。
**其他多用户特性**
> 大部分Android特性，包括Android凭据的存储、在线账户数据库、设备管理策略和VPN等都支持多用户策略。
