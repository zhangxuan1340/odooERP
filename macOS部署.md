# macOS 开发环境部署
<!-- TOC -->autoauto- [macOS 开发环境部署](#macos-开发环境部署)auto- [环境要求](#环境要求)auto    - [准备工作](#准备工作)auto        - [安装Python](#安装python)auto        - [安装Nodejs](#安装nodejs)auto        - [安装Python虚拟环境](#安装python虚拟环境)auto        - [安装Odoo 所需Python依赖](#安装odoo-所需python依赖)auto        - [安装jpep库](#安装jpep库)auto        - [安装Wkhtmltopdf](#安装wkhtmltopdf)auto        - [安装PostgreSQL](#安装postgresql)autoauto<!-- /TOC -->
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
