
说明：
	使用Docker构建的开发环境：Nginx、MySQL、Redis、PHP5.4、PHP5.6、PHP7.2、支持xdebug、Composer

下图是这个Docker下的开发环境的结构 

![Demo Image](./dnmp.png)

### 特点
1. 完全开源
2. 支持多版本PHP切换（PHP5.4、PHP5.6、PHP7.1、PHP7.2...)
3. 支持绑定任意多个域名
4. 支持HTTPS和HTTP/2
5. PHP源代码位于host中
6. MySQL data位于host中
7. 所有配置文件可在host中直接修改
8. 所有日志文件可在host中直接查看
9. 内置完整PHP扩展安装命令
10. 实现一次配置，Windows、Linux、MacOs皆可用

github仓库地址：https://github.com/heguangzhi/dnmp


### 一、快速使用 
* 根据系统Linux,MacOS 安装 `docker` and `docker-compose`;

* dnmp.tar为全部的Docker Dnmp images文件，使用Docker加载images 
    
```
$docker load -i dnmp.tar 
$dcoker images 
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
dnmp_php-5.6        latest              5058dd413ff1        2 days ago          685MB
dnmp_php-5.4        latest              3d5f34a10142        2 days ago          854MB
dnmp_php-7.2        latest              c44343fb86f1        2 days ago          739MB
dnmp_php-7.1        latest              c44343fb86f1        2 days ago          739MB
nginx               alpine              2dea9e73d89e        6 days ago          18MB
redis               latest              c5355f8853e4        13 days ago         107MB
mysql               5.7              5195076672a7        3 weeks ago         371MB


```

可以看到多个image文件已经load进入Docker

* 启动docker容器:

```
 $docker-compose -f docker-compose.yml up -d 
 $docker ps 
    
CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                      NAMES
9f0d62123d9b        nginx:alpine          "nginx -g 'daemon ofΒ   10 minutes ago      Up 10 minutes       0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp   lnmp_nginx_1
a29c79eda632        lnmp_php-7.2:latest   "docker-php-entrypoiΒ   10 minutes ago      Up 10 minutes       9000/tcp                                   lnmp_php-7.2_1
f0ae150c249b        lnmp_php-5.6:latest   "docker-php-entrypoiΒ   10 minutes ago      Up 10 minutes       9000/tcp                                   lnmp_php-5.6_1
fcd1d0e37b9c        lnmp_php-5.4:latest   "php-fpm"                10 minutes ago      Up 10 minutes       9000/tcp                                   lnmp_php-5.4_1
d155fbb9f5c8        redis:latest          "docker-entrypoint.sΒ   10 minutes ago      Up 10 minutes       0.0.0.0:6379->6379/tcp                     lnmp_redis_1
2c40800cda39        mysql:5.7             "docker-entrypoint.sΒ   10 minutes ago      Up 10 minutes       0.0.0.0:3306->3306/tcp                     lnmp_mys

```   

可以看到容器已经启动了。
    
* 在浏览其中localhost:

![Demo Image](./snapshot.png)

* 目录结构
 
 目录结构如下：

```
.
├── docker-compose.yml          容器启动配置文件
├── conf                        配置目录
│   ├── mysql                   MySQL配置文件目录
│   │   └── my.cnf              MySQL配置文件
│   ├── nginx                   Nginx配置文件目录
│   │   ├── conf.d              站点配置文件目录
│   │   │   ├── certs           SSL认证文件、密钥和加密文件目录
│   │   │   │   └── site2       站点2的认证文件目录
│   │   │   ├── www.site1.com.conf  站点1 Nginx配置文件
│   │   │   └── www.site2.com.conf  站点2 Nginx配置文件
│   │   └── nginx.conf          Nginx通用配置文件
│   └── php-5.4                 PHP配置目录
│   │   ├── php-fpm.d           PHP-FPM配置目录
│   │   │   └── www.conf        PHP-FPM配置文件
│   │   └── php.ini             PHP配置文件
│   └── php-5.6                 PHP配置目录
│   │   ├── php-fpm.d           PHP-FPM配置目录
│   │   │   └── www.conf        PHP-FPM配置文件
│   │   └── php.ini             PHP配置文件
│   └── php-7.1                 PHP配置目录
│   │   ├── php-fpm.d           PHP-FPM配置目录
│   │   │   └── www.conf        PHP-FPM配置文件
│   │   └── php.ini             PHP配置文件
│   └── php-7.2                PHP配置目录
│       ├── php-fpm.d           PHP-FPM配置目录
│       │   └── www.conf        PHP-FPM配置文件
│       └── php.ini             PHP配置文件
├── log                         日志目录
│   ├── mysql                   MySQL日志目录
│   ├── nginx                   Nginx日志目录
│   └── php-fpm                 PHP-FPM日志目录
├── mysql                       MySQL数据文件目录
└── www                         站点根目录
│   ├── www.sit1.com            站点1根目录
│   └── www.sit2.com            站点2根目录
│ 
└── php                         PHP Dockfiles
│ 
└── other                       Nginx、Redis、MySQL Dockfiles
```

* 站点部署

默认加了两个站点：www.site1.com（同localhost）和www.site2.com, 在www站点目录中，在Nginx conf 目录也有相应的配置文件

要在本地访问这两个域名，需要修改本机hosts文件，添加以下两行：

```
127.0.0.1 www.site1.com
127.0.0.1 www.site2.com
```

其中，www.site2.com为支持SSL/https和HTTP/2的示例站点。

因为站点2的SSL采用自签名方式，所以浏览器有安全提示，继续访问就可以了，自己的站点用第三方SSL认证证书替换即可。

如果只用到站点1，把站点2相关的目录和配置文件删除：

```
./conf/nginx/conf.d/certs/site2/
./conf/nginx/conf.d/site2.conf
./www/site2/

```

重启容器内的Nginx生效：

```
$docker-compose  restart nginx 

```
nginx 为docker-compose.yml 规定的服务名（service）


* 切换不同的PHP版本 

找到nginx虚机配置文件
例如： www.site1.com.conf 修改nginx配置文件：

```
location ~ \.php$ {
    fastcgi_pass   fpm-5.6:9000;
    fastcgi_index  index.php;
    include        fastcgi_params;
    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
}

```
仅需把fastcgi_pass改成：fpm-7.1:9000 ; PHP-FPM的侦听主机改成：Nginx links PHP-FPM service的别名，在docker-compose.yml文件里面我们设置为service别名，例如 php-7.2 为fpm-7.2、php-7.1为fpm-7.1。

具体查看 docker-compose.yml文件 

```
links:
    - php-7.2:fpm-7.2
    - php-7.1:fpm-7.1
    - php-5.6:fpm-5.6
    - php-5.4:fpm-5.4
    - 
```

Nginx容器启动的时候，就会自动修改nginx容器的/etc/hosts，让fpm-* 指向php-* 容器的IP。

修改之后，重启容器中的nginx：

```
$docker-compose  restart nginx 

```

* 使用MySQL数据库

docker-compose.yml文件中，指定了MySQL数据库root用户的密码为123456。 
在主机终端中输入密码，就可以进入MySQL命令行。

```
$ mysql -h 127.0.0.1 -u root -p

```
也可以通过数据库管理软件 navicat 连接MySQL数据库。

在PHP代码中的使用方式与在主机中使用稍有不同，如下：


```
$pdo = new PDO('mysql:host=mysql;dbname=****', 'root', '123456');

```

其中，host的值就是在docker-compose.yml里面指定的MySQL服务的别名。

这是因为PHP代码是在PHP-FPM容器中，PHP-FPM容器启动时会自动在/etc/hosts中加上：

172.17.0.2 mysql 11e55f91c4c3 dnmp_mysql_1
就是说，mysql自动指向了MySQL容器动态生成的IP。

* 使用Redis

Redis使用和MySQL类似。在主机和容器内部都通过地址127.0.0.1，端口6379访问。PHP则是跨容器访问，host参数用redis（links指定的名称），端口用6379。


### 二、构建镜像文件

* 根据系统Linux,MacOS 安装 `docker` and `docker-compose`;


* 国内镜像仓库
Docker默认从DockerHub仓库下载镜像，更换途径，使用阿里云的加速Docker仓库

注册一个阿里云账号，然后访问阿里云的Docker镜像仓库，能找到加速器地址。

https://cr.console.aliyun.com/cn-beijing/mirrors 


1. 安装／升级Docker客户端
推荐安装1.10.0以上版本的Docker客户端，参考文档 docker-ce

2. 配置镜像加速器
针对Docker客户端版本大于 1.10.0 的用户

您可以通过修改daemon配置文件/etc/docker/daemon.json来使用加速器

```

sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://i37cj3ld.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker

Docker 1.10以下请看：https://yq.aliyun.com/articles/29941。

```

* build PHP不同的版本的镜像

```
$docker-compose  -f   docker-compose-build.yml build 
```
经过比较长时间的构建过程。

查看构建好的镜像文件：

```
$docker images 

REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
dnmp_redis          latest              1f8625486a7c        2 hours ago         83.4MB
dnmp_mysql          latest              7c4721471bbf        2 hours ago         372MB
dnmp_nginx          latest              873baaf32c10        2 hours ago         18.6MB
dnmp_php-7.1        latest              37d8af74edcf        2 hours ago         700MB
dnmp_php-7.2        latest              8c71f5b4c965        2 hours ago         710MB
dnmp_php-5.6        latest              82af6947b093        3 hours ago         668MB
dnmp_php-5.4        latest              4ebb508214b0        3 hours ago         856MB
mysql               5.7                 75576f90a779        2 days ago          372MB
nginx               alpine              36f3464a2197        9 days ago          18.6MB
php                 5.6-fpm             8d3dc6499e61        12 days ago         344MB
php                 7.1-fpm             72197c69469a        12 days ago         358MB
php                 7.2-fpm             1d5598895841        12 days ago         367MB
redis               latest              f06a5773f01e        2 weeks ago         83.4MB
php                 5.4-fpm             1b825a5a7ecd        2 years ago         469MB```

```

3.构建并启动DNMP

```
$docker-compose  up -d 
$docker ps 

```

```

CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                                      NAMES
ed007d8d69b2        dnmp_nginx:latest     "nginx -g 'daemon ofΒ   2 hours ago         Up 2 hours          0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp   dnmp_nginx_1
e0a4a2530663        dnmp_mysql:latest     "docker-entrypoint.sΒ   2 hours ago         Up 2 hours          0.0.0.0:3306->3306/tcp                     dnmp_mysql_1
00b0cca0455c        dnmp_redis:latest     "docker-entrypoint.sΒ   2 hours ago         Up 2 hours          0.0.0.0:6379->6379/tcp                     dnmp_redis_1
4a713f865c8b        dnmp_php-7.1:latest   "docker-php-entrypoiΒ   2 hours ago         Up 2 hours          9000/tcp                                   dnmp_php-7.1_1
d6cf990c932e        dnmp_php-5.4:latest   "php-fpm"                2 hours ago         Up 2 hours          9000/tcp                                   dnmp_php-5.4_1
64e03cd823ed        dnmp_php-5.6:latest   "docker-php-entrypoiΒ   2 hours ago         Up 2 hours          9000/tcp                                   dnmp_php-5.6_1
ddd712165aec        dnmp_php-7.2:latest   "docker-php-entrypoiΒ   2 hours ago         Up 2 hours          9000/tcp                                   dnmp_php-7.2_1


```

4.打包dnmp集成开发环境images

```

$docker save dnmp_php-5.4 dnmp_php-5.6 dnmp_php-7.1  dnmp_php-7.2  dnmp_redis dnmp_mysql dnmp_nginx > dnmp.tar 

``` 



  


