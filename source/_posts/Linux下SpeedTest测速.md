---
title: Linux下SpeedTest测速
tags:
  - Linux
  - 测速
  - 命令
  - code
date: 2017-06-07 17:25:21
---

```
wget https://raw.githubusercontent.com/sivel/speedtest-cli/master/speedtest.py

chmod +x speedtest.py

测试结果：$ ./speedtest.py
Retrieving speedtest.net configuration...
Testing from Psychz (104.???.???.???)...
Retrieving speedtest.net server list...
Selecting best server based on ping...
Hosted by Time Warner Cable (Los Angeles, CA) [36.79 km]: 316.038 ms
Testing download speed................................................................................
Download: 0.91 Mbit/s
Testing upload speed................................................................................................
Upload: 2.81 Mbit/s
```
<!--more-->

列出服务器：
```
$ ./speedtest.py --list
Retrieving speedtest.net configuration...
7017) C3Network.Inc (Diamond Bar, CA, United States) [3.21 km]
11603) Ultimate Internet Access (Ontario, CA, United States) [19.54 km]
2953) Atlantic Metro (Los Angeles, CA, United States) [36.79 km]
5827) Time Warner Cable (Los Angeles, CA, United States) [36.79 km]
3226) Race Communications (Los Angeles, CA, United States) [36.79 km]
10392) Speedtest.net (Los Angeles, CA, United States) [36.79 km]
3883) SingTel (Los Angeles, CA, United States) [36.79 km]
6047) Phyber Communications (Los Angeles, CA, United States) [36.79 km]
5861) WebNX (Los Angeles, CA, United States) [36.79 km]
5303) Host Duplex (Los Angeles, CA, United States) [36.79 km]
5602) HugeServer Networks, LLC (Los Angeles, CA, United States) [36.79 km]
7456) QuadraNet, Inc (Los Angeles, CA, United States) [36.79 km]
7244) psychz.net (Los Angeles, CA, United States) [36.79 km]
3864) California Internet Solutions (Los Angeles, CA, United States) [36.79 km]
5905) GigeNET (Los Angeles, CA, United States) [36.79 km]
7048) FPT Telecom (Los Angeles, CA, United States) [36.79 km]
6248) GorillaServers (Los Angeles, CA, United States) [36.79 km]
11482) Race Communications (Los Angeles, CA, United States) [36.79 km]
10269) Interoute VDC (Los Angeles, CA, United States) [36.79 km]
9916) fdcservers.net (Los Angeles, CA, United States) [36.79 km]
2426) I2B Networks Inc (San Diego, CA, United States) [158.00 km]
2969) ScaleMatrix (San Diego, CA, United States) [158.00 km]
948) FastServ Networks LLC (San Diego, CA, United States) [158.00 km]
```

指定服务器：
```
p$ ./speedtest.py --server 11603
Retrieving speedtest.net configuration...
Testing from Psychz (104.???.???.???)...
Retrieving speedtest.net server list...
Selecting best server based on ping...
Hosted by Ultimate Internet Access (Ontario, CA) [19.54 km]: 194.453 ms
Testing download speed................................................................................
Download: 1.20 Mbit/s
Testing upload speed................................................................................................
Upload: 3.95 Mbit/s
```

分享：
```
$ ./speedtest.py --share
Retrieving speedtest.net configuration...
Testing from Psychz (104.???.???.???)...
Retrieving speedtest.net server list...
Selecting best server based on ping...
Hosted by Ultimate Internet Access (Ontario, CA) [19.54 km]: 197.445 ms
Testing download speed................................................................................
Download: 1.87 Mbit/s
Testing upload speed................................................................................................
Upload: 3.65 Mbit/s
Share results: http://www.speedtest.net/result/6358097368.png
```

![测试结果](http://www.speedtest.net/result/6358097368.png)

![](http://www.speedtest.net/result/6360291791.png)
