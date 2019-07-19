今天试着在suse服务器上的centOS系统部署dpvs,是按照github的教程一步一步来进行的:https://github.com/iqiyi/dpvs

记录一下完整的过程以及中间遇到的坑。

第一步：

 ``git clone https://github.com/iqiyi/dpvs.git
 cd dpvs``

第二步：下载dpdk-17.11.2.tar.xz放在dpvs的目录下，可以通过命令：

`wget https://fast.dpdk.org/rel/dpdk-17.11.2.tar.xz   # download from dpdk.org if link failed.`

但由于我这里wget命令无法使用，我直接把dpdk-17.11.2.tar.xz下载到本地然后用xftp上传。

解压dpdk-17.11.2.tar.xz：

`tar vxf dpdk-17.11.2.tar.xz`

`$ cd dpvs`
`$ cp patch/dpdk-stable-17.11.2/*.patch dpdk-stable-17.11.2/`
`$ cd dpdk-stable-17.11.2/`
`$ patch -p 1 < 0001-PATCH-kni-use-netlink-event-for-multicast-driver-par.patch`
`$ patch -p1 < 0002-net-support-variable-IP-header-len-for-checksum-API.patch`

第三步：dpdk编译安装：

`$ cd dpdk-stable-17.11.2/`
`$ make config T=x86_64-native-linuxapp-gcc`
`$ make` 
`$ export RTE_SDK=$PWD`

这里在进行make的时候遇到了一些问题 ：

![img](https://img-blog.csdnimg.cn/20190715222939467.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dZSDE5OTUxMjIw,size_16,color_FFFFFF,t_70)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

### 这里报错name.h：no such file or dictory

经过网上查阅资料，发现要安装依赖包numactl-devel ；于是尝试yum install numactl-devel，又出现以下问题：

![img](https://img-blog.csdnimg.cn/20190715223204918.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

又尝试rpm安装依然不行：

![img](https://img-blog.csdnimg.cn/20190715223331982.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

后来终于找到解决办法，要先更新yum源，步骤如下：

`$ mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup`

`$ cd /etc/yum.repos.d/`

`$ wget http://mirrors.163.com/.help/CentOS7-Base-163.repo` 
`#下载163的yum源配置文件，放入/etc/yum.repos.d/ ，由于我的wget命令无法使用，所以直接下载到本地然后上传到服务器`

`$ yum makecache  #运行yum makecache生成缓存`

`$ yum -y update`

然后再安装依赖numactl-devel，

`$ yum install numactl-devel`

安装之后再执行make即可。

第四步：设置DPDK hugepage

#### for NUMA machine
`$ echo 8192 > /sys/devices/system/node/node0/hugepages/hugepages-2048kB/nr_hugepages`
`$ echo 8192 > /sys/devices/system/node/node1/hugepages/hugepages-2048kB/nr_hugepages`

`$ mkdir /mnt/huge`
`$ mount -t hugetlbfs nodev /mnt/huge`

在执行第二条时候出错，没有解决。

第五步：绑定网卡。

`$ modprobe uio`
`$ cd dpdk-stable-17.11.2`

`$ insmod build/kmod/igb_uio.ko`
`$ insmod build/kmod/rte_kni.ko`

 `#察看网卡状态`
`$ ./usertools/dpdk-devbind.py --status`  

`#将网卡ens192停掉`
`$ ifconfig ens192 down  # ens192 is 0000:0b:00.0` 
`#绑定ens192网卡`
`$ ./usertools/dpdk-devbind.py -b igb_uio 0000:0b:00.0`

第六步：编译dpvs

`$ cd dpdk-stable-17.11.2/`
`$ export RTE_SDK=$PWD`
`$ cd dpvs`

`$ make`
`$ make install`

这里进行make之前又需要安装两个依赖包：

`$ yum install openssl-devel`

`$ yum install popt-devel`

编译成功之后打开bin目录：

`$ ls bin/`
`dpip  dpvs  ipvsadm  keepalived`

第七步：启动dpvs：

`$ cp conf/dpvs.conf.single-nic.sample /etc/dpvs.conf`

`$ cd dpvs/bin`

`$ ./dpvs &`

在执行./dpvs &的时候再次遇到问题：

![img](https://img-blog.csdnimg.cn/20190715225516713.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dZSDE5OTUxMjIw,size_16,color_FFFFFF,t_70)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

![img](https://img-blog.csdnimg.cn/20190715225353246.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dZSDE5OTUxMjIw,size_16,color_FFFFFF,t_70)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

没有解决.