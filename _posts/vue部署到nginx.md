# 安装nginx

## 安装nginx所需环境

```powershell
yum install gcc-c++  
yum install -y pcre pcre-devel  
yum install -y zlib zlib-devel  
yum install -y openssl openssl--devel 
```

## **下载tar.gz安装包和解压** 

官网：[http://nginx.org/en/download.html ](http://nginx.org/en/download.html)  这里本人下载的是 nginx-1.14.2.tar.gz

上传的目录是在/usr/local下, cd 到/usr/local 目录，解压命令：

```
tar -xvf nginx-1.14.2.tar.gz
```

我们进入到cd nginx-1.14.2到下去配置。

## nginx配置

依次执行

```
./configure
make && make install
```

## 查看安装目录

```
whereis nginx
```

可以看到我安装目录是在/usr/local/nginx/ 

## 启动nginx

```
cd /usr/local/nginx/sbin/

//接着执行
./nginx 
```

输入我们的服务器ip地址：如图成功了

<img src="C:\Users\F\AppData\Roaming\Typora\typora-user-images\image-20220331155849675.png" alt="image-20220331155849675" style="zoom:67%;" />

# 部署vue项目

## 打包

```
//通过该命令将vue项目打包 生成dist
npm run bulid
```

## 修改nginx配置

打开nginx下conf文件夹下的nginx.conf文件，在这里进行nginx的核心配置。

```
server {
        listen       8089; //nginx的访问端口，改完后即通过此端口访问项目
        server_name  localhost;//这个保持不变即可

        location / {
        try_files $uri $uri/ /index.html;//划重点，这一步能使用户访问Vue时刷新界面不至于404，即是正常的实现了vue的history路由功能
            root   /usr/local/nginx/html/dist;//将默认访问根节点定位到项目目录，即是我们之前拷贝过来的Vue项目。

            index  index.html index.htm;
        autoindex_exact_size off;   //文件大小从KB开始显示
            charset utf-8;          //显示中文
            add_header 'Access-Control-Allow-Origin' '*'; //允许来自所有的访问地址
            add_header 'Access-Control-Allow-Credentials' 'true';
            add_header 'Access-Control-Allow-Methods' 'GET, PUT, POST, DELETE, OPTIONS'; //支持请求方式
            add_header 'Access-Control-Allow-Headers' 'Content-Type,*';
        }
    
    location /api{//配置代理，和开发时的devServer中配置保持一致即可，如有多个即配置多个location
           rewrite ^/api/(.*)$ /$1 break;
           include uwsgi_params;
           proxy_set_header   Host             $host;
           proxy_set_header   x-forwarded-for  $remote_addr;
           proxy_set_header   X-Real-IP        $remote_addr;
           proxy_pass  http://127.0.0.1:8080; // 接口地址，如果在同一个服务器上，可以直接这样写,后面端口号写接口的端口号
        }
}
```

## 把dist文件夹放入

<img src="C:\Users\F\AppData\Roaming\Typora\typora-user-images\image-20220331160200848.png" alt="image-20220331160200848" style="zoom: 80%;" />

放入到上面配置的红色箭头位置

## 重启nginx

然后 ./nginx -s reload  重新加载配置一下nginx.conf 文件

然后访问 ip地址:上面配置的端口号  就可以了