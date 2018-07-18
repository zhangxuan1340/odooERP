# macOS 开发环境部署
<!-- TOC -->

- [macOS 开发环境部署](#macos-开发环境部署)
- [环境要求](#环境要求)
        - [安装Python](#安装python)
        - [安装Nodejs](#安装nodejs)
        - [安装Python虚拟环境](#安装python虚拟环境)
        - [安装jpep库](#安装jpep库)
        - [安装Wkhtmltopdf](#安装wkhtmltopdf)

<!-- /TOC -->
# 环境要求
* 系统环境：macOS
* Python版本：Python3.6以下
* WKHTML版本：0.12.5
* Xocde 安装最新版本

macOS平台部署Odoo相当的简易提前布置好 [Brew](https://brew.sh)

打开 macOS 自带终端 部署Odoo。
``` bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
安装Brew的同时开启新的终端 Clone Odoo。
``` bash
mkdir odoo
cd odoo
git clone https://github.com/odoo/odoo.git -b 11.0 --depth=1 odoo11
```
克隆最新提交版本，同时只克隆分支Odoo11.

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
pip3 install -r odoo11/requirements.txt  #获取Odoo11的依赖
#requirements中有部分软件需要二次编译，会报错要注释掉。
pip3 install Babel decorator docutils ebaysdk feedparser gevent greenlet html2text Jinja2 lxml Mako MarkupSafe mock num2words ofxparse passlib Pillow psutil psycogreen psycopg2 pydot pyparsing PyPDF2 pyserial python-dateutil python-openid pytz pyusb PyYAML qrcode reportlab requests six suds-jurko vatnumber vobject Werkzeug XlsxWriter xlwt xlrd 
```

### 安装jpep库
``` bash
brew install libjpeg
```

### 安装Wkhtmltopdf 

进入 [官网](https://wkhtmltopdf.org/downloads.html)选择系统版本进行下载安装。

