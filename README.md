# vps-tutorial
一些 vps 常用命令

## 1 调整时间

查看当前系统时间

```sh
date -R
# 输出信息：Sat, 26 Jun 2021 18:03:36 +0800

# 把时区设成上海
timedatectl list-timezones | grep Shanghai
timedatectl set-timezone Asia/Shanghai
```



## 2 更新 Linux 软件

```sh
apt update		# 更新
apt upgrade -y	# 更新 + 更新时进行废弃处理
reboot

apt install wget curl screen vim ufw net-tools sudo -y
```



## 3 安全防护

- 【端口】：将 SSH 远程登录端口修改为【非 22 端口】
- 【用户名】：建立【非 root】的新用户、并禁用 root 用户 SSH 远程登录
- 【密码】：SSH 启用 RSA 密钥验证登录、同时禁用密码验证登录



### 3.1 将 SSH 修改为非 22 端口

```sh
vim /etc/ssh/sshd_config

# 默认 SSH 端口 22 改为其它
Port 2333

# 重启 SSH 服务
systemctl restart ssh.service
```

- vim 显示行号：`:/set nu`
- vim 搜索：`:/关键词`，查找下一个`n`，查找上一个 `N`
- vim 保存退出：`:wq`
- vim 不保存强制退出：`:q!`



### 3.2 建立非 root 的新用户

用户标识符说明

![User-file-identifier-description](https://raw.githubusercontent.com/Himly/vps-tutorial/master/img/User-file-identifier-description.jpg)

```sh
# 查看所有用户
cat /etc/passwd

# 添加用户 tom 并为其创建家目录
useradd -m tom -c tom
# 设置 tom 用户的密码
passwd tom
```

相关参数说明：

- `-g` ：指定用户的主组的编号信息
- `-s` ：指定用户可以使用的Shell类型：`/bin/bash` ：给人用的，`/sbin/nologin` ：给软件用的，比如mysql
- `-G` ：指定附属组的ID编号，如果有多个附属组，可以使用逗号隔开 `useradd -G 1000,1001 ..`
- `-d` ：指定用户的家，/home/itheima，`useradd -d /itheima itheima`
- `-u` ：指定用户的ID编号，默认是自动生成的
- `-c `：comment，注释信息，scj:x:uid:gid:宋楚杰的账号
- `-n` ：取消以用户名称命名的组信息
- `-M` : 不要创建用户的家目录



把用户 tom 加入 sudo 名单里，让他有资格借用root的神力

```sh
visudo
```

在 `User Privilege Specification` 下加入一行即可。

```markdown
tom     ALL=(ALL) NOPASSWD: ALL
```

![User-privilege-specification](https://raw.githubusercontent.com/Himly/vps-tutorial/master/img/User-privilege-specification.jpg)

> <span style=color:red>**注意：**</span>
>
> 我要特别说明的是`NOPASSWD`这个设置，它的意思是`tom`用户临时使用`root`权限时，不用额外输入密码。**这与一般的安全建议相反**。我之所以如此推荐，是因为很多新人不顾危险坚持使用`root`账号就是因为用`root`时不用重复输入密码、觉得轻松。“两害相权取其轻”，我认为【直接用`root`用户的风险】大于【使用`sudo` 时不用输密码的风险】，所以做了以上的建议。
>
> 如果你希望遵守传统习惯、每次使用`sudo`时要输入密码，那这一行改成 `tom ALL=(ALL:ALL) ALL` 即可。

账号切换演示：

```sh
# tom --> root
sudo -i
# root --> tom
su - tom
```



### 3.3 SSH 启用 RSA 密钥验证登录

**1）为普通用户申请 SSH 登录密钥对**

```sh
# 切换到普通用户 tom
su - tom

ssh-keygen -t rsa -b 4096 -C "tom@debian.localdomain" -N "123456"
#或
ssh-keygen \
    -t rsa \	//指定密钥类型
    -b 4096 \	//指定密钥长度
    -C "azureuser@myserver" \	//添加注释，便于识别
    -f ~/.ssh/id_rsa \			//指定密钥文件的名字
    -N mypassphrase				//密钥密码
    
# 下载 id_rsa 私钥文件
cd .ssh && ls -alh
# 下载 id_rsa 私钥文件了吗?
# 下载 id_rsa 私钥文件了吗?
# 下载 id_rsa 私钥文件了吗?

# 本地私钥文件
rm -f id_rsa && ls -alh

# 修改 id_rsa.pub 名字并修改为 600 权限
mv id_rsa.pub authorized_keys && chmod 600 authorized_keys && ls -alh
```



**2）使用 RSA 密钥验证登录并禁用密码登录**

```sh
sudo vim /etc/ssh/sshd_config

# 修改以下内容
PubkeyAuthentication yes
PasswordAuthentication no
```



**3）禁用 root 用户 SSH 远程登录**

```sh
sudo vim /etc/ssh/sshd_config

# 修改以下内容
PermitRootLogin no
```



**4）重启 SSH 服务**

```sh
sudo systemctl restart ssh
```
