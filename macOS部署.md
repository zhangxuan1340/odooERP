# macOS 开发环境部署

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
brew install python3 python3-pip
```
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
```

### 安装Wkhtmltopdf 

进入 [官网](https://wkhtmltopdf.org/downloads.html)选择系统版本进行下载安装。

