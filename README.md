# ServerStatus中文版：   

* ServerStatus中文版是一个酷炫高逼格的云探针、云监控、服务器云监控、多服务器探针~。
* 在线演示：https://tz.cloudcpp.com    

# 目录介绍：

* autodeploy    自动部署
* clients       客户端文件
* server        服务端文件
* web           网站文件  

# 更新说明：

* 20190418, cntaoge 简化安装命令
* 20190129, cppla 降低CPU占用                            

# 自动部署：

【服务端】：
<li>一、选安装宝塔或者其它WEB应用，我这里的安装环境是CENTOS 7.X MINI安装，如果已经在架好站点在使用中了，可以直接跳过此步骤。在这以宝塔面板为例：</li>
<br>yum install -y wget && wget -O install.sh http://download.bt.cn/install/install_6.0.sh && bash install.sh</br>
<li>二、配置WEB应用：</li>
<br>LNMP 模式
<br>Nginx 1.15 （必装）
<br>Ftpd (可不装)
<br>MySQL 5.6 (如果不架其它站点数据库也可以不用装)
<br>PHP 7.0 （如果不架其它站点不装也可以）
<br>phpmyadmin (不装数据库的话这个也可以不用装)
<br>极速安装
<li>三、建立站点如：test.com  我以这个域名为例，你可以根据自己的站点路么进行修改下面命令里的网站路径</li>
<p>
<br>timedatectl set-timezone Asia/Shanghai
<br>rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
<br>yum update
<br>yum install golang -y
<br>cd /home
<br>git clone -b master https://github.com/cntaoge/ServerStatus.git
<br>cd ServerStatus/server
<br>make
<br>firewall-cmd --zone=public --add-port=35601/tcp --permanent 
<br>firewall-cmd --reload
<br>\cp -rf /home/ServerStatus/web/* /www/wwwroot/test.com
<br>chmod -R a+x /home/ServerStatus
<br>chmod -R 755 /home/ServerStatus
<br>chmod 777 /home/ServerStatus/run_ss.sh
<br>echo "nohup bash /home/ServerStatus/run_ss.sh >/dev/null 2>&1 &" >>/etc/rc.d/rc.local
<br>chmod +x /etc/rc.d/rc.local
<br>echo "nohup python /home/ServerStatus/clients/client-linux.py >/dev/null 2>&1 &" >>/etc/rc.d/rc.local
<br>chmod +x /etc/rc.d/rc.local
<br>reboot
<br>#
<p>

# 相关开源项目，感谢： 

* cppla：https://github.com/cppla/ServerStatus
* ServerStatus：https://github.com/BotoX/ServerStatus
* mojeda: https://github.com/mojeda 
* mojeda's ServerStatus: https://github.com/mojeda/ServerStatus
* BlueVM's project: http://www.lowendtalk.com/discussion/comment/169690#Comment_169690
