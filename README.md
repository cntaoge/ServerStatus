# ServerStatus中文版：   

<br>* ServerStatus中文版是一个酷炫高逼格的云探针、云监控、服务器云监控、多服务器探针~。
<br>* 在线演示：https://tz.cloudcpp.com    
<br>本人交流联系方法在主站上：www.gxnnhxy.com
# 目录介绍：
<br>* autodeploy    自动部署
<br>* clients       客户端文件
<br>* server        服务端文件
<br>* web           网站文件  
# 更新说明：
<br>* 20190418, cntaoge 修改安装流程命令                        

【服务端】：
<li>一、先安装宝塔或者其它WEB应用，我这里的安装环境是CENTOS 7.X MINI安装，如果已经在架好站点在使用中了，可以直接跳过此步骤。在这以宝塔面板为例：</li>
<p>
<pre>yum install -y wget && wget -O install.sh http://download.bt.cn/install/install_6.0.sh && bash install.sh</pre></br>
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
<li>三、在宝塔面板上建立站点如：test.com  我以这个域名为例，宝塔里建好的站点路径为/www/wwwroot/test.com你可以根据自己的站点路径进行修改下面命令里的网站路径,修改完路径后直接复制粘贴就行了</li>
<br> 把下面的命令全部复制到记事本里进行编辑，把三处网站的路径修改成你自己的。修改好之后粘贴到SSH客户端命令行上。
<pre>timedatectl set-timezone Asia/Shanghai
rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
yum update
yum install git -y
yum install golang -y
cd /home
git clone -b master https://github.com/cntaoge/ServerStatus.git
chmod -R 755 /home/ServerStatus/
cd ServerStatus/server
make
firewall-cmd --zone=public --add-port=35601/tcp --permanent 
firewall-cmd --reload
\cp -rf /home/ServerStatus/web/* /www/wwwroot/test.com   #(★需要把里面的网站路径目录修改成你自己的)
chown -R www:www /www/wwwroot/test.com    #(★需要把里面的网站路径目录修改成你自己的)
chmod -R a+x /www/wwwroot/test.com     #(★需要把里面的网站路径目录修改成你自己的)
echo "nohup bash /home/ServerStatus/run_ss.sh >/dev/null 2>&1 &" >>/etc/rc.d/rc.local
chmod +x /etc/rc.d/rc.local
echo "nohup python /home/ServerStatus/clients/client-linux.py >/dev/null 2>&1 &" >>/etc/rc.d/rc.local
chmod +x /etc/rc.d/rc.local
ok</pre>
<p>
<li>四、前端面板需要修改配置文件：
<br>①需要把run_ss.sh里面的网站路径目录修改成你自己的" --web-dir=/www/wwwroot/test.com "
<br>
<pre>vi /home/ServerStatus/run_ss.sh </pre>  
<br> 启动前端面板新进程命令（后台运行）：          
<pre>nohup bash /home/ServerStatus/run_ss.sh >/dev/null 2>&1 &</pre>.
<br> 启动本机监控进程命令（后台运行）： 
<pre>nohup python /home/ServerStatus/clients/client-linux.py >/dev/null 2>&1 &</pre>
<br> 到这里，你可以使用你的站点域名进行访问了：http://你绑定的域名
<li>五、前端面板配置其它说明：
<br>调试前时可直接使用调试命令，用ctrl+c 中止：   
<pre>bash /home/ServerStatus/run_ss.sh</pre>
<br>前端面板 服务器配置文件 s01为本机、依次添加修改，username 名称不能与其它节点相同。
<pre>vi /home/ServerStatus/server/config.json</pre>
<pre>		{
			"username": "s02",  #后端连接用户名，前后端要一致
			"name": "WEB001号",   #节点名称
			"type": "xen",   #虚拟化加构 ovz  kvm   xen 之类的
			"host": "host2",   #主机位置排序，按顺序增加如：节点2修改为 host2;节点3修改为 host3
			"location": "阿里云香港",  #位置
			"password": "USER_DEFAULT_PASSWORD"    #后端节点连接密码，前端后端密码要一致
		},</pre>
<br> 查看所有进程信息：
<pre>ps e -A</pre>    命令间有空格，大小写之区别
<br> 终止进程：
<pre>kill 1234</pre>   命令间有空格然后加所属的进程ID号
<br> 查看指定进程：
<pre>ps 1234</pre>   命令间有空格然后加所属的进程ID号
<p>
<li>六、其它后端节点安装方法,复制下面命令</li>
<p>
<pre>timedatectl set-timezone Asia/Shanghai
rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
yum update
yum install git -y
yum install golang -y
cd /home
git clone -b master https://github.com/cntaoge/ServerStatus.git
chmod -R 755 /home/ServerStatus/
echo "nohup python /home/ServerStatus/clients/client-linux.py >/dev/null 2>&1 &" >>/etc/rc.d/rc.local
chmod +x /etc/rc.d/rc.local
ok</pre>
<p>
<br>修改为你主控端服务器的IP地址及二号节点的用户名、密码
<br>
<pre>vi /home/ServerStatus/clients/client-linux.py</pre>
<p>
<br>SERVER = "127.0.0.1"    #前端面板服务器IP地址或者域名
<br>PORT = 35601      #前端面板服务器设置的监听端口
<br>USER = "s02"    #前端面板里为这台后端节点分配的用户名
<br>PASSWORD = "USER_DEFAULT_PASSWORD"    #前端面板里为这台后端节点分配的密码
<p>
<br>设置完成 ESC + :  wq 回车存盘退出
<br> 
<br> 节点配置完连接参数后，这里你可以选择重启或者直接运行程序，不过我建议是重启检验一下开机启动是否设置成功。启动进程，在面板上就会显示出来了
<br> 启动后端节点新进程命令（后台运行）： 
<pre>nohup python /home/ServerStatus/clients/client-linux.py >/dev/null 2>&1 &</pre>
<br> 调试监控状态可直接使用命令，ctrl+c 中止：
<pre>python /home/ServerStatus/clients/client-linux.py</pre>
<br> 查看所有进程信息：    命令间有空格，大小写之区别
<pre>ps e -A</pre>
<br> 终止进程：  命令间有空格然后加所属的进程ID号
<pre>kill 1234</pre> 
<br> 查看指定进程：命令间有空格然后加所属的进程ID号
<pre>ps 1234</pre>   
<br>

# 相关开源项目，感谢： 
* cppla：https://github.com/cppla/ServerStatus
* ServerStatus：https://github.com/BotoX/ServerStatus
* mojeda: https://github.com/mojeda 
* mojeda's ServerStatus: https://github.com/mojeda/ServerStatus
* BlueVM's project: http://www.lowendtalk.com/discussion/comment/169690#Comment_169690
