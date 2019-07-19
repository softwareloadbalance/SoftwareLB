在安装nginx的过程中，它所依赖的包有gcc，gcc-c++，zlib，openssl，pcre。

**第一步：安装gcc。**

安装gcc的过程感觉难度有点大，就请导师提供了SLES-12-SP3-DVD-x86-GM-DVD1.iso文件。

在ip地址为10.2.1.214的服务器创建了一个media目录，接下来执行的步骤如下所示：

`cd /media/`

`cd /data`

`mount -t  iso9660 -o loop SLES-12-SP3-DVD-x86-GM-DVD1.iso /media`

`zypper ar /media/ suse12sp3iso`

然后到 /etc/zypp/repos.d/里面删除一个文件：删除短的那个。

安装gcc：`zypper in gcc`

​                 `zypper in gcc-c++`

然后可以编写一个c程序测试一下gcc安装是否成功。

**第二步：安装pcre**

`wget http://downloads.sourceforge.net/project/pcre/pcre/8.35/pcre-8.35.tar.gz`

`cd pcre-8.35`

`./configure`

`make`

`make install`

**第三步：安装zlib**

`wget http://zlib.net/zlib-1.2.11.tar.gz`

`cd zlib-1.2.11`

`./configure`

`make`

`make install`

**第四步：安装openssl**

`wget https://www.openssl.org/source/openssl-1.0.2s.tar.gz`

`cd openssl-1.0.2s`

`./config`

`make`

`make install`

**第五步：下载安装nginx**

`wget http://nginx.org/download/nginx-1.2.8.tar.gz`

`tar-zxvf nginx-1.2.8.tar.gz`

`cd nginx-1.2.8`

`./configure && sudo make && sudo make install` 

**第六步：启动nginx**

`cd nginx-1.2.8`

`sudo /usr/local/nginx/sbin/nginx`

或者 

`cd nginx-1.2.8`

`cd objs`

`./nginx`

**第七步：通过浏览器访问ip地址10.2.1.214出现如下界面，表示成功：**

![img](https://img-blog.csdnimg.cn/20190711085719800.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1dZSDE5OTUxMjIw,size_16,color_FFFFFF,t_70)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

**第八步：****nginx作反向代理访问四个web服务器（这里搭了静态页面作为web服务器，ip地址分别为10.2.1.216:80； 10.2.1.217:80； 10.2.1.218:80； 10.2.1.219:80）**

**修改nginx配置文件：**

`cd /`

`cd usr/local/nginx/conf`

`vi nginx.conf`

`在nginx的配置文件中,进行如下配置:`

`upstream webServer{`
            `server 10.2.1.216:80;`
            `server 10.2.1.217:80;`
            `server 10.2.1.218:80;`
            `server 10.2.1.219:80;`
        `}`

`location / {`
            `proxy_pass http://webServer;`
            `#root   html;`
            `index  index.html index.htm;`
        `}`

之后重启nginx：

进入nginx的sbin目录下：

`cd /usr/local/nginx/sbin`

`./nginx -s reload`

然后通过浏览器访问10.2.1.214:

![img](https://img-blog.csdnimg.cn/20190711195549436.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

![img](https://img-blog.csdnimg.cn/20190711195607736.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

![img](https://img-blog.csdnimg.cn/20190711195635659.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

![img](https://img-blog.csdnimg.cn/20190711195717848.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

刷新会看到这四个界面轮流出现。

