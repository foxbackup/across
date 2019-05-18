# Some useful scripts

# BBR

使用root用户登录，运行以下命令：
```
wget --no-check-certificate https://raw.githubusercontent.com/foxbackup/across/master/bbr.sh && chmod +x bbr.sh && ./bbr.sh
```
安装完成后，脚本会提示需要重启 VPS，输入 y 并回车后重启。
重启完成后，进入 VPS，验证一下是否成功安装最新内核并开启 TCP BBR，输入以下命令：
```
uname -r
```
查看内核版本，显示为最新版就表示 OK 了

sysctl net.ipv4.tcp_available_congestion_control
返回值一般为：
net.ipv4.tcp_available_congestion_control = bbr cubic reno
或者为：
net.ipv4.tcp_available_congestion_control = reno cubic bbr
sysctl net.ipv4.tcp_congestion_control
返回值一般为：
net.ipv4.tcp_congestion_control = bbr
sysctl net.core.default_qdisc
返回值一般为：
net.core.default_qdisc = fq


# KMS

使用root用户登录，运行以下命令：

wget --no-check-certificate https://raw.githubusercontent.com/foxbackup/across/master/kms.sh && chmod +x kms.sh && ./kms.sh
安装完成后，输入以下命令查看端口号 1688 的监听情况

netstat -nxtlp | grep 1688
返回值类似于如下这样就表示 OK 了：

tcp        0      0 0.0.0.0:1688                0.0.0.0:*                   LISTEN      3200/vlmcsd         
tcp        0      0 :::1688                     :::*                        LISTEN      3200/vlmcsd 
本脚本安装完成后，会将 KMS 服务加入开机自启动。

使用命令：
```
启动：/etc/init.d/kms start
停止：/etc/init.d/kms stop
重启：/etc/init.d/kms restart
状态：/etc/init.d/kms status
```
卸载方法：
使用 root 用户登录，运行以下命令：
```
./kms.sh uninstall
```
如何使用 KMS 服务
KMS 服务，用于在线激活 VOL 版本的 Windows 和 Office。
激活的前提是你的系统是批量授权版本，即 VL 版，一般企业版都是 VL 版。而 VL 版本的镜像一般内置 GVLK key，用于 KMS 激活。
下面列表里面含有的产品的 VL 版本或者能使用 key 进入 KMS 通道的产品，都支持使用 KMS 激活。

Office 2019 & Office 2016：https://docs.microsoft.com/en-us/DeployOffice/vlactivation/gvlks
Office 2013：https://technet.microsoft.com/zh-cn/library/dn385360.aspx
Office 2010：https://technet.microsoft.com/zh-cn/library/ee624355(v=office.14).aspx
Windows：https://docs.microsoft.com/zh-cn/windows-server/get-started/kmsclientkeys

使用管理员权限运行 cmd 查看系统版本，命令如下：
```
wmic os get caption
```
使用管理员权限运行 cmd 安装从上面列表得到的 key，命令如下：
```
slmgr /ipk xxxxx-xxxxx-xxxxx-xxxxx-xxxxx
```
使用管理员权限运行 cmd 将 KMS 服务器地址设置为你自己的 IP 或 域名，后面最好再加上端口号（:1688），命令如下：
```
slmgr /skms Your IP or Domain:1688
```
注意：本脚本所做的工作就是此步骤。当你的 KMS 服务出于启动状态，那么此处就可以设置为你自己的 KMS 服务器地址。
使用管理员权限运行 cmd 手动激活系统，命令如下：
```
slmgr /ato
```
关于 Office 的激活，要求必须是 VOL 版本，否则无法激活。
找到你的 Office 安装目录，32 位默认一般为 C:\Program Files (x86)\Microsoft Office\Office16
64 位默认一般为 C:\Program Files\Microsoft Office\Office16
Office16 是 Office 2016，Office15 就是 Office 2013，Office14 就是 Office 2010。
打开以上所说的目录，应该有个 OSPP.VBS 文件。
使用管理员权限运行 cmd 进入 Office 目录，命令如下：
```
cd "C:\Program Files (x86)\Microsoft Office\Office16"
```
使用管理员权限运行 cmd 注册 KMS 服务器地址：
```
cscript ospp.vbs /sethst:Your IP or Domain
```
使用管理员权限运行 cmd 手动激活 Office，命令如下：
```
cscript ospp.vbs /act
```
注意： KMS 方式激活，其有效期只有 180 天。
每隔一段时间系统会自动向 KMS 服务器请求续期，请确保你自己的 KMS 服务正常运行。

lsmod | grep bbr
l2tp.sh
=======

- Description: Auto install L2TP VPN for CentOS6+/Debian7+/Ubuntu12+
- Intro: https://teddysun.com/448.html
```bash
Usage: l2tp [-l,--list|-a,--add|-d,--del|-m,--mod|-h,--help]

| Bash Command     | Description                  |
|------------------|------------------------------|
| l2tp -l,--list   | List all users               |
| l2tp -a,--add    | Add a user                   |
| l2tp -d,--del    | Delete a user                |
| l2tp -m,--mod    | Modify a user password       |
| l2tp -h,--help   | Print this help information  |
```

bbr.sh
======

- Description: Auto install latest kernel for TCP BBR
- Intro: https://teddysun.com/489.html

kms.sh
======

- Description: Auto install KMS Server
- Intro: https://teddysun.com/530.html

bench.sh
========

- Description: Auto test download & I/O speed script
- Intro: https://teddysun.com/444.html
```bash
Usage:

| No.      | Bash Command                    |
|----------|---------------------------------|
| 1        | wget -qO- bench.sh | bash       |
| 2        | curl -Lso- bench.sh | bash      |
| 3        | wget -qO- 86.re/bench.sh | bash |
| 4        | curl -so- 86.re/bench.sh | bash |
```

backup.sh
=========

- You must modify the config before run it
- Backup MySQL/MariaDB/Percona datebases, files and directories
- Backup file is encrypted with AES256-cbc with SHA1 message-digest (option)
- Auto transfer backup file to Google Drive (need install `gdrive` command) (option)
- Auto transfer backup file to FTP server (option)
- Auto delete Google Drive's or FTP server's remote file (option)
- Intro: https://teddysun.com/469.html

```bash
Install gdrive command step:

For x86_64: 
wget -O /usr/bin/gdrive http://dl.lamp.sh/files/gdrive-linux-x64
chmod +x /usr/bin/gdrive

For i386: 
wget -O /usr/bin/gdrive http://dl.lamp.sh/files/gdrive-linux-386
chmod +x /usr/bin/gdrive
```

ftp_upload.sh
=============

- You must modify the config before run it
- Upload file(s) to FTP server
- Intro: https://teddysun.com/484.html

unixbench.sh
============

- Description: Auto install unixbench and test script
- Intro: https://teddysun.com/245.html

pptp.sh(Deprecated, DO NOT USE)
===================

- Description: Auto Install PPTP for CentOS 6
- Intro: https://teddysun.com/134.html

Copyright (C) 2013-2019 Teddysun <i@teddysun.com>
