Forsaken-Mail
==============
A self-hosted disposable mail service.

[Online Demo](http://disposable.dhc-app.com)

### Installation

#### Setting up your DNS correctly

In order to receive emails, your smtp server address should be made available somewhere. Two records should be added to your DNS records. Let us pretend that we want to receive emails at ```*@subdomain.domain.com```:
* First an MX record: ```subdomain.domain.com MX 10 mxsubdomain.domain.com```. This means that the mail server for addresses like ```*@subdomain.domain.com``` will be ```mxsubdomain.domain.com```.
* Then an A record: ```mxsubdomain.domain.com A the.ip.address.of.your.mailin.server```. This tells at which ip address the mail server can be found.

You can fire up Mailin (see next section) and use an [smtp server tester](http://mxtoolbox.com/diagnostic.aspx) to verify that everything is correct.

#### Let's Go
general way:
```
npm install && npm start
```
if you want to run this inside a docker container
```
docker build -t denghongcai/forsaken-mail .
docker run --name forsaken-mail -d -p 25:25 -p 3000:3000 denghongcai/forsaken-mail
```
Open your browser and type in
```
http://localhost:3000
```

Enjoy!


Forsaken Mail临时邮箱搭建方法

说明：Forsaken Mail是一个临时邮箱系统，可以供任何人接受邮件，即收即毁，支持自定义邮箱地址前缀，这里就说下Docker和NPM两种安装教程，任选一种即可，有兴趣或者有需求的可以玩玩。


提示：如果你不想自己搭建，只是想使用自己域名，可以直接使用本站服务，只需使将自己的域名创建域名cname记录到 mail.0du.win 等待生效即可.


需求
1、VPS需要开启25端口，这个直接发工单要主机商开启。
2、域名解析，如果你想邮件地址格式都为*@xx.com的形式，则为xx.com设置MX记录，需要添加以下2条解析记录。


#MX记录
xx.com MX 10 mx.xx.com

#A记录 
mx.xx.com A 服务器IP
#PS：简化操作可以直接xx.com  A 服务器IP


NPM安装
Github地址：https://github.com/denghongcai/forsaken-mail

1、安装NPM

#Debian/Ubuntu系统
```
curl -sL https://deb.nodesource.com/setup_10.x | bash -
apt-get install -y nodejs git screen
```
#Centos系统
```
curl -sL https://rpm.nodesource.com/setup_10.x | bash -
yum install nodejs git screen -y
```

2、安装Forsaken Mail

#下载项目源码
```
git clone https://github.com/beiona/forsaken-mail.git
cd forsaken-mail
```

#安装项目需要的库
```
npm install
```

#安装pm2工具
```
npm install -g pm2
```

#禁用postfix和sendmail
```
killall sendmail
/etc/init.d/postfix stop
chkconfig --level 2345 postfix off
chkconfig --level 2345 sendmail off
```

#启动项目
```
pm2 start bin/www
```

#设置开机启动
```
pm2 startup
pm2 save
```
打开http//mx.xx.com:3000就可以查看Forsaken Mail邮箱界面了，如果你打不开界面，可能还需要开启防火墙端口，一般该情况在CentOS系统上出现最多，这里就说下CentOS开启操作。

#Centos 6系统
```
iptables -I INPUT -p tcp --dport 3000 -j ACCEPT
service iptables save
service iptables restart
```

#CentOS 7系统
```
firewall-cmd --zone=public --add-port=3000/tcp --permanent 
firewall-cmd --reload
```
Docker安装
1、安装Docker

#CentOS 6
```
rpm -iUvh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
yum update -y
yum -y install docker-io
service docker start
chkconfig docker on
```
#CentOS 7、Debian、Ubuntu
```
curl -sSL https://get.docker.com/ | sh
systemctl start docker
systemctl enable docker
```
2、安装Forsaken Mail
```
docker run --name forsaken-mail -d -p 25:25 -p 3000:3000 denghongcai/forsaken-mail
```
安装成功后打开http//mx.xx.com:3000就可以了，如果打不开界面请参考NPM安装步骤。

配置Https访问
如果你不习惯使用http//mx.xx.com:3000，或者想使用Https域名访问主界面，那我们可以使用Caddy反代。

这里所使用的域名只能是上面设置MX记录的xx.com，并提前将域名A记录解析到服务器IP。

1、安装Caddy
使用命令：
```
wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubiBackup/doubi/master/caddy_install.sh && chmod +x caddy_install.sh && bash caddy_install.sh
#备用地址
wget -N --no-check-certificate https://www.moerats.com/usr/shell/Caddy/caddy_install.sh && chmod +x caddy_install.sh && bash caddy_install.sh
```
2、配置Caddy
```
#以下全部内容是一个整体，请修改2个域名后一起复制到SSH运行！
echo "xx.com {
 gzip
 tls admin@xkx.me
 proxy / mx.xx.com:3000
}" > /usr/local/caddy/Caddyfile
```
tls参数会自动帮你签发ssl证书，如果你要使用自己的ssl，改为tls /root/xx.crt /root/xx.key即可。后面为ssl证书路径。

3、启动Caddy
```
/etc/init.d/caddy start
```
最后可以打开https://xx.com访问了。

开放25。3000两个端口
1、安装NPM
#Centos系统
```
curl -sL https://rpm.nodesource.com/setup_10.x | bash -
yum install nodejs git screen -y
```

2、安装Forsaken Mail
#下载项目源码
```
git clone https://github.com/beiona/forsaken-mail.git
cd forsaken-mail
```

#安装项目需要的库
```
npm install
```

#安装pm2工具
```
npm install -g pm2
```

#启动项目
```
pm2 start bin/www
```

#设置开机启动
```
pm2 startup
pm2 save
```

dns解析
a类型 mail。域名。com 解析到ip
mx类型 mail。域名。com 解析到 mail。域名。com
