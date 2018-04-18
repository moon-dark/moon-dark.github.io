---
title: Installing Redis on a Raspberry Pi for caching
date: 2018-04-13 22:29:00
tags:
  - code
  - linux
  - server
---
![](/img/rpi-redis.jpeg)

[Installing Redis on a Raspberry Pi for caching](https://habilisbest.com/install-redis-on-your-raspberrypi)
***
If you want to configure caching to speed up web apps you may want to run on your Raspberry Pi. Or, it could be that some web apps, even require caching for their stable operation.

Whatever the case may be, caching could be a life saver, when installed and configured properly.

In this article, I will show how I install Redis on my Raspberry Pis. First of all, we need to download the latest stable release of Redis.
<!-- more -->
Prepare to download Redis source and build it on your Raspberry Pi. It may sound spooky complicated, but it's just as simple as running 3 Linux bash commands.

```
cd ~
wget http://download.redis.io/redis-stable.tar.gz
tar xzf redis*
cd redis*

sudo make
make test
sudo make install PREFIX=/usr
```
At this point, you've compiled Redis Now, let's install it to a folder accessible to all users Create a directory

``sudo mkdir /etc/redis``

Install Compiled Redis there

``sudo cp redis.conf /etc/redis/``

It is time to clean-up
```
cd ..
sudo rm -Rf redis*
```
Create a user without Home directory and without the possibility to login into your system. As you are going to run Redis as this user.

``sudo adduser --system --group --disabled-login redis --no-create-home --shell /bin/nologin --quiet``

Check the shadow file to see if Redis user were created.
```
cat /etc/passwd | grep redis
```
Edit the Redis configuration file to setup caching.
```
sudo nano /etc/redis/redis.conf
```
Use nano's CTRL+W function to search for the following entries. some are already set. But, there are still few values to set. For example,

- daemonize yes to run Redis as a Deamon.
- stop-writes-on-bgsave-error no, Redis stops on failure and requires a manual restart. Using this setting Redis will not stop on failure.
- maxmemory 70M, RAM is limited on a RaspberryPi, so 70 Mb is enough for most of my needs. If you need more memory, adjust this setting later.
```
bind 127.0.0.1
port 6379
daemonize yes
stop-writes-on-bgsave-error no
rdbcompression yes
maxmemory 50M
maxmemory-policy allkeys-lru
```
At this point, you can create a script to run your Redis server.

Create a directory, this script will be executed from.

``sudo mkdir -p /var/run/redis``

Make Redis user the owner of this directory.

``sudo chown -R redis /var/run/redis``

Create the script.

``sudo nano /etc/init.d/redis-server``

Paste the following.
```
#! /bin/sh
### BEGIN INIT INFO
# Provides: redis-server
# Required-Start: $syslog $remote_fs
# Required-Stop: $syslog $remote_fs
# Should-Start: $local_fs
# Should-Stop: $local_fs
# Default-Start: 2 3 4 5
# Default-Stop: 0 1 6
# Short-Description: redis-server - Persistent key-value db
# Description: redis-server - Persistent key-value db
### END INIT INFO

PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
DAEMON=/usr/bin/redis-server
DAEMON_ARGS=/etc/redis/redis.conf
NAME=redis-server
DESC=redis-server

RUNDIR=/var/run/redis
PIDFILE=$RUNDIR/redis-server.pid

test -x $DAEMON || exit 0

if [ -r /etc/default/$NAME ]
then
. /etc/default/$NAME
fi

. /lib/lsb/init-functions

set -e

case "$1" in
  start)
echo -n "Starting $DESC: "
mkdir -p $RUNDIR
touch $PIDFILE
chown redis:redis $RUNDIR $PIDFILE
chmod 755 $RUNDIR

if [ -n "$ULIMIT" ]
then
ulimit -n $ULIMIT
fi

if start-stop-daemon --start --quiet --umask 007 --pidfile $PIDFILE --chuid redis:redis --exec $DAEMON -- $DAEMON_ARGS
then
echo "$NAME."
else
echo "failed"
fi
;;
  stop)
echo -n "Stopping $DESC: "
if start-stop-daemon --stop --retry forever/TERM/1 --quiet --oknodo --pidfile $PIDFILE --exec $DAEMON
then
echo "$NAME."
else
echo "failed"
fi
rm -f $PIDFILE
sleep 1
;;

  restart|force-reload)
${0} stop
${0} start
;;

  status)
status_of_proc -p ${PIDFILE} ${DAEMON} ${NAME}
;;

  *)
echo "Usage: /etc/init.d/$NAME {start|stop|restart|force-reload|status}" >&2
exit 1
;;
esac

exit 0
```
Make this script executable.

``sudo chmod +x /etc/init.d/redis-server``

Add this script to be run as a command

``sudo update-rc.d redis-server defaults``

If everything set correctly, the Redis server could now be manipulated through this command

``sudo service redis-server restart``

Now you may see the results of your actions Check the

``redis-server -v``

Make sure it listening on 127.0.0.1

``netstat -antp``

Make sure the Redis process is running as the redis user.

``ps aux | grep redis``

You may now enjoy your Redis caching server installation on your Raspberry Pi.

### (Optional) Installing PHPRedis
***
If you are planning on using Redis as a cache driver for your web app running on PHP, you may want to install PHPRedis as well. Otherwise, this is completely optional.

First, Install few needed packages

``sudo apt-get install libtool php7.0-dev``

or php-dev, if you are using php 5. Go to your user’s home directory.
```
cd ~
git clone https://github.com/phpredis/phpredis.git

# If this step doesn’t work, please make sure you have GIT installed
sudo apt-get install git

cd phpredis
git checkout php7
git pull
```
Now, phpize the files.

``phpize``

And start building it from sources.
```
./configure

make clean

make

sudo make install
```
After the installation is over, clean-up the user’s home directory.
```
cd ..

rm -rf phpredis
```
The following commands easier executed with root user.
```
su

# Add few extensions

echo "extension=redis.so" > /etc/php/7.0/mods-available/redis.ini

ln -sf /etc/php/7.0/mods-available/redis.ini /etc/php/7.0/fpm/conf.d/20-redis.ini

ln -sf /etc/php/7.0/mods-available/redis.ini /etc/php/7.0/cli/conf.d/20-redis.ini

# Exit from root user.

exit
```
Restart services
```
service php7.0-fpm restart

service nginx restart
```
Now, you may enjoy your installation of PHPRedis.
