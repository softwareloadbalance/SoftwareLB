一、安装keepalived

必须要用tools压缩包中的来安装，不要用其他开源版本

```bash
cd /usr/local/src/lvs-fullnat-synproxy/tools/keepalived
```

```bash
./configure --with-kernel-dir="/lib/modules/`uname -r`/build";
```

报错:

![1563497652538](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1563497652538.png)

解决: 执行yum install -y openssl openssl-devel

再次执行又报错:

![1563497794443](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1563497794443.png)

解决: 执行 yum install popt-devel

再次执行./configure --with-kernel-dir="/lib/modules/`uname -r`/build"

![1563498259780](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1563498259780.png)

make

![1563498340777](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1563498340777.png)

make install

```[root@localhost keepalived]# make install
make -C keepalived install
make[1]: Entering directory/usr/local/src/lvs-fullnat-synproxy/tools/keepalived/keepalived'install -d /usr/local/sbininstall -m 700 ../bin/keepalived /usr/local/sbin/install -d /usr/local/etc/rc.d/init.dinstall -m 755 etc/init.d/keepalived.init /usr/local/etc/rc.d/init.d/keepalivedinstall -d /usr/local/etc/sysconfiginstall -m 755 etc/init.d/keepalived.sysconfig /usr/local/etc/sysconfig/keepalivedinstall -d /usr/local/etc/keepalived/samplesinstall -m 644 etc/keepalived/keepalived.conf /usr/local/etc/keepalived/install -m 644 ../doc/samples/* /usr/local/etc/keepalived/samples/install -d /usr/local/share/man/man5install -d /usr/local/share/man/man8install -m 644 ../doc/man/man5/keepalived.conf.5 /usr/local/share/man/man5install -m 644 ../doc/man/man8/keepalived.8 /usr/local/share/man/man8make[1]: Leaving directory /usr/local/src/lvs-fullnat-synproxy/tools/keepalived/keepalived'
make -C genhash install
make[1]: Entering directory/usr/local/src/lvs-fullnat-synproxy/tools/keepalived/genhash'install -d /usr/local/bininstall -m 755 ../bin/genhash /usr/local/bin/install -d /usr/local/share/man/man1install -m 644 ../doc/man/man1/genhash.1 /usr/local/share/man/man1
make[1]: Leaving directory `/usr/local/src/lvs-fullnat-synproxy/tools/keepalived/genhash
```



```bash
cp -a bin/genhash /usr/local/bin/
cp -a bin/keepalived /sbin/
cp -a keepalived/etc/init.d/keepalived.init /etc/init.d/keepalived
cp -a keepalived/etc/keepalived/keepalived.conf /etc/keepalived/keepalived.conf
```

这一步报错:

![1563498740082](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1563498740082.png)

解决方法：去 /etc 目录下创建keepalived文件夹

cd  /etc

mkdir keepalived

再 cd /usr/local/src/lvs-fullnat-synproxy/tools/keepalived

```bash
cp -a keepalived/etc/keepalived/keepalived.conf /etc/keepalived/keepalived.conf
```

就可以了。

```bash
cp -a keepalived/etc/init.d/keepalived.sysconfig /etc/sysconfig/keepalived
```

二 安装ipvsadm

`cd /usr/local/src/lvs-fullnat-synproxy/tools/ipvsadm`

`make`

`make install`

![1563499826671](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1563499826671.png)

安装 quaage 

```
cd cd /usr/local/src/lvs-fullnat-synproxy/tools/quagga
 ./configure --disable-ripd --disable-ripngd --disable-bgpd --disable-watchquagga --disable-doc  --enable-user=root --enable-vty-group=root --enable-group=root --enable-zebra --localstatedir=/var/run/quagga
```

```
 make
 make install
```

三  系统自身参数配置

1。

```bash
打开irqbalance
# service irqbalance start
# chkconfig --level 2345 irqbalance on
```

![1563499879531](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1563499879531.png)

2。

```bash
vi /etc/sysctl.conf
# configure for lvs
net.ipv4.conf.all.arp_ignore = 1
net.ipv4.conf.all.arp_announce = 2
net.core.netdev_max_backlog = 500000
net.ipv4.ip_forward = 1
```

