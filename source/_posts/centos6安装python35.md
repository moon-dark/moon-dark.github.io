---
title: centos6安装python3.5、pip3.5、easy_install-3.5
tags:
  - Linux
  - Python
  - code
date: 2017-04-29 20:43:27
---
centos6自带python2.6版本，根据需要，安装python3.5、easy_install-3.5、pip3.5

安装依赖
```
yum groupinstall -y Development tools
yum install -y zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
```

### 安装Python3.5
```
wget https://www.python.org/ftp/python/3.5.2/Python-3.5.2.tar.xz
xz -d Python-3.5.2.tar.xz
tar xvf Python-3.5.2.tar

cd Python-3.5.2
./configure --prefix=/usr/local
make && make install && echo OK

# 切换Python版本
make altinstall
# 强制安装pip版本
python -m pip install --upgrade --force-reinstall pip
```

安装完后的python的可执行文件目录是 /usr/local/bin

安装完后的python安装目录路径是 /usr/local/lib/python3.5

此时可通过python3.5命令验证安装
```
python3.5
Python 3.5.2 (default, Jul 11 2016, 09:15:05)
[GCC 4.4.7 20120313 (Red Hat 4.4.7-17)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>

[root@localhost ~]# python3.5 -V
Python 3.5.2
```

### 安装 setuptools(easy_install-3.5) + pip3.5
注意：一定要使用 python3.5 来执行相关命令
```
# 下载setuptools,easy_install 的安装包
wget https://bootstrap.pypa.io/ez_setup.py

#安装easy_install-3.5
python3.5 ez_setup.py

#安装完成后/usr/local/bin/目录下出现easy_install-3.5

#安装pip3.5
easy_install-3.5 pip

#安装完成后/usr/local/bin/目录下出现pip3.5

#升级pip3.5版本
pip3.5 install --upgrade pip
```

至此easy_install-3.5、pip3.5安装完成
