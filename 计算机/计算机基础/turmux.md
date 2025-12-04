

以下是 Termux 的详细使用指南，涵盖安装、配置及常见操作：

---

# Termux 终极指南：在 Android 上打造 Linux 终端环境

## 一、基础配置
### 1. 初始化环境
```bash
# 更新源列表并升级所有包
pkg update && pkg upgrade -y

# 安装核心工具集
pkg install -y coreutils curl wget git nano vim

# 配置存储权限（允许访问手机存储）
termux-setup-storage
```

### 2. 换国内源（加速下载）
```bash
# 替换为清华源
sed -i 's@^\(deb.*stable main\)$@#\1\ndeb https://mirrors.tuna.tsinghua.edu.cn/termux/termux-packages-24 stable main@' $PREFIX/etc/apt/sources.list

# 更新生效
pkg update
```

## 二、开发环境搭建
### 1. Python 开发栈
```bash
pkg install python -y
pip install --upgrade pip
pip install numpy requests flask

# 验证安装
python -c "import flask; print(flask.__version__)"
```

### 2. Node.js 环境
```bash
pkg install nodejs-lts -y  # 安装 LTS 版本
npm install -g yarn pm2

# 创建测试项目
mkdir node-app && cd node-app
npm init -y
npm install express
```

### 3. 数据库服务
```bash
# 安装 MariaDB
pkg install mariadb -y

# 初始化数据库
mysql_install_db
mysqld_safe -u $(whoami) &
mysql -u $(whoami)
```

## 三、高级功能配置
### 1. SSH 远程访问
```bash
# 安装 OpenSSH
pkg install openssh -y

# 设置密码（默认为设备锁屏密码）
passwd

# 启动 SSH 服务（默认端口8022）
sshd
```

从 PC 连接：
```bash
ssh -p 8022 手机IP地址
```

### 2. 运行 Web 服务器
```bash
# 使用 Python 快速启动（端口8080）
python -m http.server 8080

# 或安装 Nginx
pkg install nginx -y
nginx
```

通过浏览器访问：`http://手机IP:8080`

## 四、系统管理技巧
### 1. 进程监控
```bash
# 安装 htop 查看资源占用
pkg install htop -y
htop
```

### 2. 定时任务
```bash
# 安装 cronie
pkg install cronie -y

# 启动服务
crond

# 添加每日备份任务（示例）
echo "0 3 * * * tar -zcf /sdcard/backup/termux-home-$(date +\%Y\%m\%d).tgz -C $HOME ." > crontab
crontab crontab
```

## 五、实用工具集
### 1. 网络诊断
```bash
pkg install nmap net-tools dnsutils -y

# 扫描本地网络
nmap -sn 192.168.1.0/24

# 查看网络接口
ifconfig
```

### 2. 文件管理
```bash
# 安装 Ranger 终端文件管理器
pkg install ranger -y
ranger

# 使用 rsync 同步文件
rsync -avz ~/Documents/ /sdcard/Backup/
```

## 六、常见问题解决
### 1. 软件包安装失败
```bash
# 清除缓存后重试
pkg clean
pkg update

# 手动下载 deb 包安装
wget https://mirror.example.com/package.deb
dpkg -i package.deb
```

### 2. 中文显示异常
```bash
# 安装中文字体和输入法
pkg install fontconfig wqy-microhei -y

# 设置环境变量
echo "export LANG=zh_CN.UTF-8" >> ~/.bashrc
source ~/.bashrc
```

---

**提示**：  
- 长按屏幕可呼出快捷菜单（复制/粘贴/新建会话）  
- 外接键盘使用 `Ctrl` 组合键（如 `Ctrl+C` 中断进程）  
- 使用 `termux-chroot` 进入完整 Linux 文件系统视图

通过 Termux，您的 Android 设备即可变身为便携式开发工作站！ 📱💻