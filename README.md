# ServerStatus中文版：   

* ServerStatus中文版是一个酷炫高逼格的云探针、云监控、服务器云监控、多服务器探针~。
* 在线演示：https://tz.cloudcpp.com    

# 目录介绍：

* autodeploy    自动部署
* clients       客户端文件
* server        服务端文件
* web           网站文件  

# 更新说明：

* 20190418, cntaoge 修改安装流程命令
* 20190129, cppla 降低CPU占用                            

# 自动部署：

【服务端】：
<li>一、先安装宝塔或者其它WEB应用，我这里的安装环境是CENTOS 7.X MINI安装，如果已经在架好站点在使用中了，可以直接跳过此步骤。在这以宝塔面板为例：</li>
<p>
<br>yum install -y wget && wget -O install.sh http://download.bt.cn/install/install_6.0.sh && bash install.sh</br>
<p>
<li>二、配置WEB应用：</li>
<br>LNMP 模式
<br>Nginx 1.15 （必装）
<br>Ftpd (可不装)
<br>MySQL 5.6 (如果不架其它站点数据库也可以不用装)
<br>PHP 7.0 （如果不架其它站点不装也可以）
<br>phpmyadmin (不装数据库的话这个也可以不用装)
<br>极速安装
<p>
<li>三、建立站点如：test.com  我以这个域名为例，宝塔里建好的站点路径/www/wwwroot/test.com你可以根据自己的站点路么进行修改下面命令里的网站路径,修改完路径后直接复制粘贴就行了</li>
<p>
<br>timedatectl set-timezone Asia/Shanghai
<br>rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
<br>yum update -y
<br>yum install golang -y
<br>cd /home
<br>git clone -b master https://github.com/cntaoge/ServerStatus.git
<br>cd ServerStatus/server
<br>make
<br>firewall-cmd --zone=public --add-port=35601/tcp --permanent 
<br>firewall-cmd --reload
<br>\cp -rf /home/ServerStatus/web/* /www/wwwroot/test.com  #(★需要把里面的网站路径目录修改成你自己的)
<br>chmod -R 755 /home/ServerStatus
<br>chmod 777 /home/ServerStatus/run_ss.sh
<br>echo "nohup bash /home/ServerStatus/run_ss.sh >/dev/null 2>&1 &" >>/etc/rc.d/rc.local
<br>chmod +x /etc/rc.d/rc.local
<br>echo "nohup python /home/ServerStatus/clients/client-linux.py >/dev/null 2>&1 &" >>/etc/rc.d/rc.local
<br>chmod +x /etc/rc.d/rc.local
<br>reboot
<br>#
<p>
<li>四、3处需要修改配置文件：
<br>
<br>vi /home/ServerStatus/run_ss.sh   #(★需要把里面的网站路径目录修改成你自己的)
<br>
<br>①前端面板 服务器配置文件
<p> 
<br>vi /home/ServerStatus/server/config.json
<p>
<br>s01为本机、依次添加修改，username 名称不能与其它节点相同。
<br>		{
<br>			"username": "s01",  #后端连接用户名，前后端要一致
<br>			"name": "node1",   #节点名称
<br>			"type": "xen",   #虚拟化加构 ovz  kvm   xen 之类的
<br>			"host": "host1",   #主机位置排序，按顺序增加如：节点2修改为 host2;节点3修改为 host3
<br>			"location": "cn",  #位置
<br>			"password": "USER_DEFAULT_PASSWORD"    #后端节点连接密码，前端后端密码要一致
<br>		},
<br>②.后端节点配置文件（服务器端本机的监控配置）
<p>
<br>vi /home/ServerStatus/clientsclient-linux.py
<p>
<br>节点配置修改 （看下面的后端节点配置说面）
<br>
<br> 启动前端面板新进程命令（后台运行）：          nohup bash /home/ServerStatus/run_ss.sh >/dev/null 2>&1 &
<br> 调试监控状态可直接使用命令，用ctrl+c 中止：   bash /home/ServerStatus/run_ss.sh
<br> 查看所有进程信息：ps e -A    命令间有空格，大小写之区别
<br> 终止进程：kill 1234   命令间有空格然后加所属的进程ID号
<br> 查看指定进程：ps 1234   命令间有空格然后加所属的进程ID号
<br> 
<p>
<li>五、其它后端节点安装方法,复制下面命令</li>
<p>
<br>timedatectl set-timezone Asia/Shanghai
<br>cd /home
<br>mkdir ServerStatus
<br>cd ServerStatus
<br>mkdir clients
<br>cd clients
<br>yum -y install wget
<br>wget --no-check-certificate -qO client-linux.py 'https://raw.githubusercontent.com/cppla/ServerStatus/master/clients/client-linux.py' && nohup python client-linux.py SERVER={$SERVER} USER={$USER} PASSWORD={$PASSWORD} >/dev/null 2>&1 &
<br>echo "nohup python /home/ServerStatus/clients/client-linux.py >/dev/null 2>&1 &" >>/etc/rc.d/rc.local
<br>chmod +x /etc/rc.d/rc.local
<br>chmod 755 /home/ServerStatus/clients/client-linux.py
<br>vi /home/ServerStatus/clients/client-linux.py
<p>
修改为你主控端服务器的IP地址及二号节点的用户名、密码
<p>
<br>SERVER = "127.0.0.1"    #前端面板服务器IP地址或者域名
<br>PORT = 35601      #前端面板服务器设置的监听端口
<br>USER = "s01"    #前端面板里为这台后端节点分配的用户名
<br>PASSWORD = "USER_DEFAULT_PASSWORD"    #前端面板里为这台后端节点分配的密码
<p>
<br>设置完成 ESC + :  wq 回车存盘退出
<br> 
<br> 节点配置完连接参数后，启去进程，在面板上就会显示出来了
<br> 启动后端节点新进程命令（后台运行）： nohup python /home/ServerStatus/clients/client-linux.py >/dev/null 2>&1 &
<br> 调试监控状态可直接使用命令，ctrl+c 中止：python /home/ServerStatus/clients/client-linux.py
<br> 查看所有进程信息：ps e -A    命令间有空格，大小写之区别
<br> 终止进程：kill 1234   命令间有空格然后加所属的进程ID号
<br> 查看指定进程：ps 1234   命令间有空格然后加所属的进程ID号
<br>这里你可以选择重启或者直接运行程序，不过我建议是重启检验一下开机启动是否设置成功。
<br>

# 相关开源项目，感谢： 

* cppla：https://github.com/cppla/ServerStatus
* ServerStatus：https://github.com/BotoX/ServerStatus
* mojeda: https://github.com/mojeda 
* mojeda's ServerStatus: https://github.com/mojeda/ServerStatus
* BlueVM's project: http://www.lowendtalk.com/discussion/comment/169690#Comment_169690
