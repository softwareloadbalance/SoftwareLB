# **web 端配置静态页面**

------



# **1.编写静态页面**



# **2.配置 nginx.conf 文件**

**nginx 的安装路径为 usr/local/nginx** 

**其下拥有 nginx.conf 配置文件**

**其中 nginx 读取配置文件可以是在 nginx.conf 或者是 conf.d （自己创建） 文件夹下的 xxx.conf 文件**

**注：conf.d**



**LB-web.conf**

```shell
server {
  server_name localhost;
  root /www/static-web;
  index index.html;
  location ~* ^.+\.(jpg|jpeg|gif|png|ico|css|js|pdf|txt){
    root /www/static-web;
  }
}
```



**静态页面放置在 /www/static-web/ 下面，index 文件为 index.html **



**修改配置如下**

**nginx.conf**

```shell
server {
        listen       80;  #监听端口--80
        server_name  localhost;  #客户端请求地址--设置为服务器ip
		root /www/static-web/; #添加语句--将80端口监听到的请求指向根目录/www/static-web/下的文件

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            #root   html;  #注释语句
            index  index.html index.htm;  #根目录下的index.html文件
        }
```



# **3.启动 nginx**

```shell
./nginx  #启动

./nginx -s reload  #重启
```



# **4.测试访问**

