# IOT2040
## **Catalogue**
* [1. Pre-install](#1)
* [2. Set Static IP](#2)
* [3. Install Mosquitto](#3)


<h2 id="1">1. Pre-install</h2>
首先下载Win32 Disk Imager和西门子的Example Image，https://sourceforge.net/projects/win32diskimager/ 和 https://support.industry.siemens.com/cs/document/109741799/simatic-iot2000-sd-card-example-image?dti=0&lc=en-WW <br>
镜像刷到卡里后，储存卡上机上电。IOT2020/2040 初始IP是192.168.200.1，所以电脑也要设置同网段的。Putty进入后，用户名：root，默认没密码。<br>
使用

```
passwd
```
来进行密码设置。

<h2 id="2">2. Set Static IP</h2>
设置静态IP地址，首先进入

```linux
cd /etc/network/
ls
nano interfaces
```
以IOT2040左边的以太网口为例，把原文件中的相关信息替换成以下内容（.50网段是华硕路由器为例，按实际环境更改）
```linux
# Wired interfaces
auto eth0
iface eth0 inet static
        address 192.168.50.188
        netmask 255.255.255.0
        gateway 192.168.50.1


auto eth1
iface eth1 inet dhcp
```
<br>接下来设置DNS服务器：
```linux
unlink /etc/resolv.conf
```
添加以下内容（.50网段还是替换成实际的）
```linux
nameserver 8.8.8.8
nameserver 192.168.50.1
nameserver 8.8.4.4
```
最后把resolv.conf这个文件变成不可更改
```linux
chattr +i /etc/resolv.conf
```
<br>创建一个新的directory（根据Siemens教程，默认的文件存储路径是tmp）
```linux
cd /home
mkdir project
```

<h2 id="3">3. Install Mosquitto</h2>
Example Image里自带的mosquitto版本太低，以下步骤可升级到最新版（版本好按照最新版修改），整个过程大概要花费15分钟完成。
```
cd
wget http://mosquitto.org/files/source/mosquitto-1.4.14.tar.gz
tar xzf mosquitto-1.4.14.tar.gz
cd mosquitto-1.4.14
# this next step takes a while
adduser mosquitto 
make WITH_SRV=no
cd test/broker
make test
cd ../../
cp client/mosquitto_pub /usr/bin
cp client/mosquitto_sub /usr/bin
cp lib/libmosquitto.so.1 /usr/lib
cp src/mosquitto /usr/bin
```
<br>接下来设置mosquitto用户名和密码
```linux
mosquitto_passwd -c /etc/mosquitto/passwd enter_your_username
nano /etc/mosquitto/mosquitto.conf
```
在mosquitto.conf的最下面添加，最后reboot重启
```
allow_anonymous false
password_file /etc/mosquitto/passwd
```
