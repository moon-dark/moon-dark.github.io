---
title: Where Is The Apache Configuration File In CentOS?
tags:
  - Linux
  - Server
  - code
date: 2018-04-15 13:42:00
---
How can we find where the default configuration files for the httpd package in CentOS are located?

By using the RPM command we can query a package to find all of its associated configuration files. Failing that we can use the find command to search the file system for these files, as we will see here.


### RPM Query Configuration Files
The options we will use with the RPM command are -q to query a package, and -c which will list the configuration files that are part of the package. As shown below we have run RPM with the -qc options on the httpd package, which is where Apache comes from in CentOS.

<!-- more --> 
```
[root@centos7 ~]# rpm -qc httpd
/etc/httpd/conf.d/autoindex.conf
/etc/httpd/conf.d/userdir.conf
/etc/httpd/conf.d/welcome.conf
/etc/httpd/conf.modules.d/00-base.conf
/etc/httpd/conf.modules.d/00-dav.conf
/etc/httpd/conf.modules.d/00-lua.conf
/etc/httpd/conf.modules.d/00-mpm.conf
/etc/httpd/conf.modules.d/00-proxy.conf
/etc/httpd/conf.modules.d/00-systemd.conf
/etc/httpd/conf.modules.d/01-cgi.conf
/etc/httpd/conf/httpd.conf
/etc/httpd/conf/magic
/etc/logrotate.d/httpd
/etc/sysconfig/htcacheclean
/etc/sysconfig/httpd
```
These are all of the default configuration files that are installed by the httpd package.

The primary Apache configuration file is /etc/httpd/conf/httpd.conf, most of the changes to Apache will be made here.

### Find Command
If you aren’t using packages to install Apache and perhaps compiled it from source instead or otherwise installed to a custom location, we can easily run find over the entire file system to find these files.
```
[root@centos7 ~]# find / -name httpd.conf
/etc/httpd/conf/httpd.conf
/usr/lib/tmpfiles.d/httpd.conf
```
The above example will search through the root of the file system ‘/’ and find every file named ‘httpd.conf’.

Don’t forget that if you edit any of the Apache configuration files you will need to reload or restart Apache to apply the changes.
