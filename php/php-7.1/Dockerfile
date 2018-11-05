
FROM centos:7.2.1511

MAINTAINER  iHero "7731226@qq.com"

RUN yum clean all 

RUN yum -y install wget 
RUN cp /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup 
#RUN wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo

RUN wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.163.com/.help/CentOS7-Base-163.repo


#使用 supervisor 管理进程
#Install supervisor  

RUN rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm 
RUN yum -y install supervisor
COPY supervisord.conf /etc/supervisord.conf


# webtatic 官方文档 https://webtatic.com/packages/php71/  

RUN rpm -Uvh   https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
RUN rpm -Uvh   http://rpms.remirepo.net/enterprise/remi-release-7.rpm



RUN yum -y install php71w php71w-cli php71w-devel php71w-common php71w-embedded php71w-fpm php71w-gd php71w-mbstring php71w-mysqlnd php71w-opcache php71w-pdo php71w-xml  php71w-pdo  php71w-pecl-apcu   php71w-pecl-redis  php71w-pecl-xdebug  php71w-pecl-memcached  php72w-pecl-imagick  --skip-broken


RUN yum clean all


#install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer


# Config TIME_ZONE
ENV TIME_ZONE=Asia/Shanghai
RUN ln -sf /usr/share/zoneinfo/$TIME_ZONE /etc/localtime && echo $TIME_ZONE > /etc/timezone


CMD ["/usr/bin/supervisord"]





