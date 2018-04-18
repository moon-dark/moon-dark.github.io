---
title: Linux常用命令
tags:
  - Linux
  - 命令
  - code
date: 2017-04-29 20:43:27
---

Panel : Config reset
```
# This worked for me in GNOME Classic on 12.04 to reset the gnome-panel configuration:
dconf reset -f /org/gnome/gnome-panel/
killall gnome-panel
```

Linux常用命令

* * *
```
Ctrl-R : 查找历史命令
Ctrl-W : 删除最后一个单词
Ctrl-U : 删除一行
cd – : 回到上一次的工作目录
man bash查找Readline Key Bindings一节来看看bash的默认热键
使用 mtr 会比使用 traceroute 要更容易定位一个网络问题。

pip install 'requests[security]'  # pip : SNIMissingWarning, InsecurePlatformWarning
```

查看占用进程端口的PID
```
sudo ss -lptn 'sport = :80'
```

crontab的文件格式
```
分 时 日 月 星期 要运行的命令

第1列分钟0～59
第2列小时0～23（0表示子夜）
第3列日1～31
第4列月1～12
第5列星期0～7（0和7表示星期天）
第6列要运行的命令

# 查看日志：tail -f /var/log/cron
```

certbot
```
./certbot-auto certonly --agree-tos --standalone --email xxx@gmail.com -d www.???.com,m.???.com
./certbot-auto certonly --agree-tos --webroot --email xxx@gmail.com -d www.???.com,m.???.com
./certbot-auto certonly --agree-tos --webroot --force-renew --email xxx@gmail.com -d www.???.com,m.???.com
/root/certbot-auto renew --force-renew

/root/certbot-auto certonly -w /etc/letsencrypt/ --webroot --agree-tos --expand -d test.domain.com
```
certbot使用crontab更新letsencrypt证书
```
* * 15 * * /root/certbot-auto renew --force-renew
```

Linux查看文件夹大小du -sh 查看当前文件夹大小
```
du -sh * | sort -n #统计当前文件夹(目录)大小，并按文件大小排序
```

Notepad++中用正则表达式匹配中文 通常正则表达式匹配中文可以利用Unicode的特点，使用[\u4e00-\u9fa5]匹配。但在Notepad++中不能正常使用。 解决方法是，首先将编码转换成Unicode（菜单-&gt;格式-&gt;转换为UTF-8，如果不转换可能匹配出错），然后使用[\x{4e00}-\x{9fa5}]就可以实现匹配中文了。
```Bash
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

****
# 服务器搭建常用命令

```Bash
pstree -p
netstat -lntp
nohup / disown
pgrep / pkill
man 7 signal
```

[Designing Integrated High Quality Linux Applications](http://www.tldp.org/HOWTO/HighQuality-Apps-HOWTO/boot.html "Designing Integrated High Quality Linux Applications")
```Bash
#build /etc/init.d/mysystem

service mysystem start
service mysystem status
service mysystem reload
service mysystem stop

update-rc.d <service> defaults
update-rc.d <service> start 20 3 4 5
update-rc.d -f <service>  remove
#chkconfig --add mysystem
#chkconfig --del mysystem
```

### SVN服务器：
```Bash
sudo svnserve -d --foreground -r /opt/svn   #Bash
```

### openvpn
```Bash
sudo apt-get install openvpn
sudo openvpn --config /path/to/config.ovpn
```

### tinyproxy
```Bash
sudo apt-get install tinyproxy
#config /etc/tinyproxy.conf
sudo iptables -I INPUT -p tcp --dport 8888 -j ACCEPT

service tinyproxy <start|stop|restart|status>
```

### [mysql server](https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-14-04 "How To Install MySQL on Ubuntu 14.04")
```Bash
sudo apt-get update
sudo apt-get install mysql-server
sudo mysql_secure_installation
sudo mysql_install_db

sudo service mysql <start|stop|restart|status>
```
