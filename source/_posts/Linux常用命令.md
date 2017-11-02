---
title: Linux常用命令
tags:
  - Linux
  - 命令
  - code
date: 2017-04-29 20:43:27
---

Linux常用命令

* * *
```
Ctrl-R : 查找历史命令
Ctrl-W : 删除最后一个单词
Ctrl-U : 删除一行
cd – : 回到上一次的工作目录
man bash查找Readline Key Bindings一节来看看bash的默认热键
使用 mtr 会比使用 traceroute 要更容易定位一个网络问题。
```

Linux查看文件夹大小du -sh 查看当前文件夹大小
```
du -sh * | sort -n #统计当前文件夹(目录)大小，并按文件大小排序```
```

Notepad++中用正则表达式匹配中文 通常正则表达式匹配中文可以利用Unicode的特点，使用[\u4e00-\u9fa5]匹配。但在Notepad++中不能正常使用。 解决方法是，首先将编码转换成Unicode（菜单-&gt;格式-&gt;转换为UTF-8，如果不转换可能匹配出错），然后使用[\x{4e00}-\x{9fa5}]就可以实现匹配中文了。
android.(text|hint).*[\x{4e00}-\x{9fa5}]+.*
```

```
date +"%Y-%m-%d %T"

file -sL /dev/sd*     //  How do I tell what sort of data (what data format) is in a file?
UUID=3c05a2da-4e6b-4ba7-855e-62914e67c99f /home/war/aosp  ext3    defaults rw 0 0    // sudo vi /etc/fstab
/dev/sda1       /work/          ntfs    defaults,umask=022,uid=1000,gid=1000          0       0
/dev/sda5       /media/         ntfs    defaults,umask=022,uid=1000,gid=1000          0       0
/dev/sda6       /aosp/          ntfs    defaults,umask=022,uid=1000,gid=1000          0       0
sudo ntfslabel /dev/sdb1 my_external   //修改ntfs卷的Label
sudo e2label  <label>          //修改ext2, ext3, and ext4卷的Label
#挂载指定挂载点
sudo rmdir /media/your_username
sudo ln -s /media /media/your_username
sudo mkfs -t ext4 /dev/sda1           //格式化ext4类型
```

```
find /path -name '*.cpp' |xargs wc -l   //统计一个项目的代码数：
grep --color='auto' -P -n "[\x80-\xFF]" file.xml   //grep for non-ASCII characters in UNIX :

:write ++enc=utf-8 russian.txt       //VIM 编码转换
 :%s/s1/s2/g                         //VIM 替换

 find . -name \*.py | xargs grep some_function
 awk ‘{ x += $3 } END { print x }’       # 求第三列的数字之和
```

reads a file passed as an argument line by line:
```
#!/bin/bash
while IFS='' read -r line || [[ -n "$line" ]]; do
    #echo "Text read from file: $line"
    grep -q \"$line\" strings.xml
    if [ $? != 0 ]; then
        echo $line, "unmatched"
    fi
done < "$1"
```
