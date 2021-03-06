# Ubuntu odoo部署
<!-- TOC -->

- [Ubuntu odoo部署](#ubuntu-odoo部署)
- [环境要求](#环境要求)
- [创立用户](#创立用户)
- [更新系统依赖并克隆odoo源码](#更新系统依赖并克隆odoo源码)
- [安装ERP所需依赖](#安装erp所需依赖)
- [设置配置文件](#设置配置文件)
        - [简要说明:](#简要说明)
            - [通过-c参数运行服务，我们可以检查配置的效果:](#通过-c参数运行服务我们可以检查配置的效果)
- [安装Services服务让系统自动启动](#安装services服务让系统自动启动)

<!-- /TOC -->
# 环境要求
对于Odoo的要求
  * 系统版本：Ubuntu
  * Python：Python3.6
  * 数据库版本：
# 创立用户
* 创建用odoo 用于同一管理odoo进程与启动
``` bash
sudo adduser odoo
```
* 给odoo赋予root权限：

``` bash
sudo vim /etc/sudoers
# User Privilege specification
root    ALL=(ALL:ALL) ALL
odoo    ALL=(ALL:ALL) ALL
wq!
```
* 保存退出注意文件只读属性修改完成后记得追加 *!* 进行强制保存。
切换用户至odoo全程进入odoo用户进行操作完成整个步骤。
``` bash
su - odoo
```
进入odoo家目录默认系统是自动进入当前账户的家目录，注意观察命令行前 *～*当然也可以自行手工进入家目录
``` bash
cd ~
```
# 更新系统依赖并克隆odoo源码
<li>1.1 更新系统

``` bash
sudo apt-get update     #注：16.04后-get可以省略
sudo apt-get upgrade    #更新系统组件
sudo apt-get install git #安装git，新版本默认安装git
```

<font color=red>注:</font>每次更新完毕后,最好重启下服务器。

<li>1.2 安装nodejs

``` bash
sudo apt-get install -y npm
sudo ln -s /usr/bin/nodejs /usr/bin/node
sudo npm install -g less
```

<li>1.3 进行克隆odoo源码

``` bash
git clone https://github.com/odoo/odoo.git -b 11.0 --depth=1 odoo11
```
odoo 克隆所指定版本可以使用 -b 11.0 如果需要克隆 Odoo12 可以使用 -b 12.0

那么如何确认自己可以克隆那个版本，我们可以查看Github里的版本库可以克隆的版本。
<img style="border-radius: 10px;" src="static\img\部署手册\branch.png" >
# 安装ERP所需依赖
<li>2.1 安装pip

``` bash
sudo apt-get install -y python3-pip libsasl2-dev build-essential libldap2-dev libsasl2-dev slapd ldap-utils python-tox  lcov valgrind
```
``` bash
#通过requirements.txt安装
cd odoo11
sudo pip3 install -r requirements.txt
#也可以复制以下命令进行安装。
sudo pip3 install Babel decorator docutils ebaysdk feedparser gevent greenlet html2text Jinja2 lxml Mako MarkupSafe mock num2words ofxparse passlib Pillow psutil psycogreen psycopg2 pydot pyparsing PyPDF2 pyserial python-dateutil python-openid pytz pyusb PyYAML qrcode reportlab requests six suds-jurko vatnumber vobject Werkzeug XlsxWriter xlwt xlrd 
```

安装成功后提示:Successfully installed

2.2 安装Postgresql数据库
``` bash
sudo apt-get install -y postgresql
sudo su - postgres
createuser --createdb --username postgres --no-createrole --no-superuser --pwprompt odoo
Enter password for new role: *****
Enter it again:*****
exit #最后使用exit进行退出
```

2.3 进入odoo11目录生成配置文件

``` bash
cd ~/odoo11
./odoo-bin -s
```

``` bash
#从启动日志可以看到，会在当前用户根目录生成配置文件 .odoorc (这个配置文件第三步会用到)
Using configuration file at /home/odoo/.odoorc #观察启动日志
```

``` bash
#开启新的终端查看进程
ps aux |grep odoo
odoo      1010  0.9  1.6 421168 66852 pts/0    Sl+  12:22   0:00 python3 ./odoo-bin -c /etc/odoo/odoo.conf
```
```bash
tcp        0      0 0.0.0.0:8069            0.0.0.0:*               LISTEN     
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:5432          0.0.0.0:*               LISTEN 
```
# 设置配置文件

``` bash
sudo mkdir /etc/odoo #创建Odoo文件夹
sudo cp /home/odoo/.odoorc /etc/odoo/odoo.conf #复制根目录配置文件到Odoo文件夹下并更名 odoo.conf
sudo chown -R odoo /etc/odoo #给目录设置权限
```

``` bash
sudo mkdir /var/log/odoo  #创建Odoo log目录
sudo chown odoo /var/log/odoo
``` 
``` bash
sudo vim /etc/odoo/odoo.conf #编辑Odoo配置文件
```
``` bash
[options]
logfile = /var/log/odoo/odoo.log
logrotate = True
```
### 简要说明:
* addons_path 使用,逗号分隔的扩展路径，会在路径中寻找模块，从左至右阅读，最左侧拥有最高的优先级。
* admin_passwd 是master主控密码，用于访问web客户端数据库管理。建议设置一个足够强的密码，设置为False能让此功能失效。
* db_user 数据库实例在服务器启动序列期间进行初始化。
* dbfilter 用于筛选可访问的数据库，它是Pythoninterpreted正则表达式. 用于不让用户选择数据库，及未验证的URLs正常工作，它应该以^dbname$设置, 例如 dbfilter=^odoo-prod$。 它支持%h和%d占位符, 用来HTTP请求的主机名和子域名.
* logfile Odoo服务日志写入的地方。系统服务的日志通常位于/var/log. 留空，或者设为False,日志则会以标准方式输出。
* logrotate = True 按天存放日志
* proxy_mode 当使用反向代理时，应当设为True。
* without_demo 在生产环境中应设为True，这样新的数据库中就不会有演示数据。
* workers 值为启用的处理器数量。
* xmlrpc_port 服务监听的端口号。默认使用8069
* data_dir 会话数据和附件存储的位置，记得备份它。
* xmlrpc-interface 设置监听的地址。默认值会监听所有端口 0.0.0.0, 在使用反向代理时，可以设置为127.0.0.1 目的是只对本地请求响应。

#### 通过-c参数运行服务，我们可以检查配置的效果:
``` bash
./odoo-bin -c /etc/odoo/odoo.conf #-c追加配置文件
```
* 此时odoo完全无启动日志，所有日志均进入log文件。
``` bash
tail -f /var/log/odoo/odoo.log 查看 日志。
```
* 安装中文字体
``` bash
sudo apt-get install ttf-wqy-zenhei -y
sudo apt-get install ttf-wqy-microhei -y
```
* 安装所需Wkhtmltopdf
``` bash
sudo mkdir Tools
cd Tools
wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.2.1/wkhtmltox-0.12.2.1_linux-trusty-amd64.deb

sudo dpkg -i wkhtmltox-0.12.2.1_linux-trusty-amd64.deb   # 会提示要安装xfonts-75dpi 等，这时候用apt-get -f install会安装这2个依赖
wget http://kr.archive.ubuntu.com/ubuntu/pool/main/libp/libpng/libpng12-0_1.2.54-1ubuntu1_amd64.deb
sudo dpkg -i libpng12-0_1.2.54-1ubuntu1_amd64.deb
sudo apt-get install -f #自动再次解决缺失依赖
sudo ln -s /usr/local/bin/wkhtmltopdf /usr/bin 
sudo ln -s /usr/local/bin/wkhtmltoimage /usr/bin
```

# 安装Services服务让系统自动启动

``` bash
sudo vi /lib/systemd/system/odoo.service #注册Odoo服务
```
``` bash
[Unit]
Description=Odoo
After=postgresql.service
[Service]
Type=simple
User=odoo
Group=odoo
ExecStart=/home/odoo/odoo11/odoo-bin -c /etc/odoo/odoo.conf
[Install]
WantedBy=multi-user.target
```
注册odoo服务 
``` bash
sudo systemctl enable odoo.service
```
开启服务
``` bash
sudo systemctl start odoo
```
查看服务状态
``` bash
sudo systemctl status odoo
```
表示Acitve（runting）状态表示服务器已经正常启动。
``` baash
● odoo.service - Odoo
   Loaded: loaded (/lib/systemd/system/odoo.service; enabled; vendor preset: enabled)
   Active: active (running) since Sun 2017-10-15 12:46:54 CST; 6s ago
 Main PID: 930 (python3)
   CGroup: /system.slice/odoo.service
           └─930 python3 /home/odoo/odoo11/odoo-bin -c /etc/odoo/odoo.conf

Oct 15 12:46:54 iZuf6jevtfmojh35fpwxb5Z systemd[1]: Started Odoo.
```
如果需要停止odoo可以使用下面的命令。
``` bash
sudo systemctl stop odoo
```
至此基本上Odoo已经全部安装完成了。
至此Odoo部署已经完成并且可以进行使用

