# macOS 开发环境部署


# 环境要求
* 系统环境：macOS
* Python版本：Python3.6以下
* WKHTML版本：0.12.5
* Xocde 安装最新版本

## 准备工作
macOS平台部署Odoo相当的简易提前布置好 [Brew](https://brew.sh)

打开 macOS 自带终端 部署Brew。
``` bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
安装Brew的同时开启新的终端 Clone Odoo。
``` bash
mkdir odoo
cd odoo
git clone https://github.com/odoo/odoo.git -b 12.0 --depth=1 odoo12
```
克隆最新提交版本，同时只克隆分支Odoo12.

### 安装Python
``` bash
brew install python3 python3-pip #macOS最新brew是Python3.7
```
brew 安装均为最新版本。
1. Brew 降级

从[Homebrew-Core](https://github.com/Homebrew/homebrew-core/commits/master/Formula/python.rb)找到需要的版本。

选择对应的 commit，点击 view 以后以 raw 模式打开，保存文件到本地。

将下载的 python.rd 文件替换到下面的 

/usr/local/Homebrew/Library/Taps/homebrew-core/Formula/python.rb

直接安装
``` bash
brew install python
```
2. 通过官方下载 PKG
通过官网链接进行下载[Python3.6](https://www.python.org/ftp/python/3.6.6/python-3.6.6-macosx10.9.pkg)版本
### 安装Nodejs
``` bash
brew install node
sudo npm install -g less
```

### 安装Python虚拟环境
``` bash
sudo pip3 install virtualenv  #pip安装Virtualenv
virtualenv env/odoo11 --python=python3.7  #最后的参数python3.7根据本机版本修改。
source env/odoo11/bin/activate   #激活虚拟环境。
```

### 安装Odoo 所需Python依赖
``` bash
pip3 install -r odoo11/requirements.txt  #获取Odoo11的依赖
```

### 安装jpep库
``` bash
brew install libjpeg
```

### 安装Wkhtmltopdf 

进入 [官网](https://wkhtmltopdf.org/downloads.html)选择系统版本进行下载安装。

### 安装PostgreSQL

``` bash
brew install postgresql   #安装Postgresql
pg_ctl -V #查看当前安装版本
#分两种服务，一种作为服务启动，还有单独启动
brew services start posrgresql #作为服务启动
pg_ctl -D /usr/local/var/postgres start #单独启动

psql postgres #访问缺省数据库
createruser -P odoo #创建新用户，其中odoo可以自定义
createdb -O odoo -Eutf8 odoo12 #创建数据库odoo12 编码 Utf-8
psql -U odoo odoo12 #以odoo用户访问odoo12 数据库
\q #退出
```
``` bash
==> postgresql
To migrate existing data from a previous major version of PostgreSQL run:
  brew postgresql-upgrade-database

To have launchd start postgresql now and restart at login:
  brew services start postgresql
Or, if you don't want/need a background service you can just run:
  pg_ctl -D /usr/local/var/postgres start
```
默认 Brew 安装完成 PostgreSQL后 brew 会给你提示，可以通过两种方式启动PostgreSQL

#### 创建 Virtual 虚拟环境

熟知Python的朋友应该都是知道的虚拟环境，还不是同时多个环境下，某个版本的包同时会影响其他项目，我们通常的做法都是在项目的根目录外创建一个环境包来隔离每个项目之间的环境。

``` bash
pip3 install virtualenv   #安装Virtualenv
cd /Volume/Data/Code/Python/Odoo #进入到项目外目录
virtualenv --no-site-packages venv #创建一个环境目录，同时加上参数--no-stie-packages 保证 Python并不会复制当前已经安装的包。
source venv/bin/activate #激活虚拟环境，激活成功后终端提示符变成 venv
exit #退出
```

#### 环境依赖

odoo依赖在克隆根目录下 requirements.txt 中，通常 Python 项目在根目录下都会有当前环境所需依赖。

``` bash
source venv/binactivate #激活虚拟环境
pip3 install odoo12/requirements.txt #安装依赖通过文件进行读取，当前Uninstall 也是可以通过文件来批量卸载。
```

#### 生成配置文件

``` bash
./odoo-bin -s  #生成配置文件在～跟目录的odoorc
cp ~/.odoorc odoo.conf
```
#### 配置参数文件
``` conf
logfile = /Volume/Data/Python/odoo/log/odoo.log
logrotate = True
```

#### 运行Odoo

``` bash
./odoo-bin -c ./odoo.conf
```
通过访问
https://localhost:8069/

#### 问题解决
ValueError: unknown locale: UTF-8
需要编辑~/.bash_profile 加入
两行
export LC_ALL=en_US.UTF-8
export LANG=en_US.UTF-8