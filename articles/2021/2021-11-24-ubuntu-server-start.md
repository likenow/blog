##  Ubuntu Server 20.04.3 LTS —添加开机启动(服务/脚本)

[toc]



### 开机启动自定义服务

在 `/etc/systemd/system` 下创建 `test.service`

```bash
[Unit]
Description=test service

[Service]
# 注意路径要明确
ExecStart=/xxx/test.sh

[Install]
WantedBy=multi-user.target
```

 重新加载系统的systemd服务文件，并启用 test.service 文件。

```bash
# 重新加载某个服务的配置文件，如果新安装了一个服务，归属于 systemctl 管理，要是新服务的服务程序配置文件生效，需重新加载。
sudo systemctl daemon-reload
# 启用
systemctl enable test.service
```

### But

**用systemd启动的服务默认是root权限，放脚本很不安全，如果只需要登录用户的权限，改成user service (systemctl --user)更好**

将systemd服务文件和我们的脚本更改权限，使其可执行。

```bash
sudo chmod 744 /xxx/test.sh
sudo chmod 664 /etc/systemd/user/test.service
```

重新加载系统的systemd服务文件，并启用我们自己写的test.service文件。

```bash
sudo systemctl daemon-reload
systemctl --user enable test.service
```





### [引用一篇文章](https://www.cnblogs.com/Areas/p/13439000.html)

> #### 修改开机启动文件：/etc/rc.local（或者/etc/rc.d/rc.local）
>
> ```bash
> # 1.编辑rc.local文件
> [root@localhost ~]# vi /etc/rc.local
> 
> # 2.修改rc.local文件，在 exit 0 前面加入以下命令。保存并退出。
> /etc/init.d/mysqld start                                         # mysql开机启动
> /etc/init.d/nginx start                                          # nginx开机启动
> supervisord -c /etc/supervisor/supervisord.conf                  # supervisord开机启动
> /bin/bash /server/scripts/test.sh >/dev/null 2>/dev/null
> 
> # 3.最后修改rc.local文件的执行权限
> [root@localhost ~]# chmod +x  /etc/rc.local
> [root@localhost ~]# chmod 755 /etc/rc.local
> ```
>
> 
>
> #### 自己写一个shell脚本
>
> 将写好的脚本（.sh文件）放到目录` /etc/profile.d/` 下，系统启动后就会自动执行该目录下的所有shell脚本。
>
> 
>
> #### 通过chkconfig命令设置
>
> ```bash
> # 1.将(脚本)启动文件移动到 /etc/init.d/或者/etc/rc.d/init.d/目录下。（前者是后者的软连接）
> mv  /www/wwwroot/test.sh /etc/rc.d/init.d
> 
> # 2.启动文件前面务必添加如下三行代码，否侧会提示chkconfig不支持。
> #!/bin/sh                          告诉系统使用的shell,所以的shell脚本都是这样
> #chkconfig: 35 20 80               分别代表运行级别，启动优先权，关闭优先权，此行代码必须
> #description: http server          自己随便发挥！！！，此行代码必须
> /bin/echo $(/bin/date +%F_%T) >> /tmp/test.log
> 
> # 3.增加脚本的可执行权限
> chmod +x  /etc/rc.d/init.d/test.sh
> 
> # 4.添加脚本到开机自动启动项目中。添加到chkconfig，开机自启动。
> [root@localhost ~]# cd /etc/rc.d/init.d
> [root@localhost ~]# chkconfig --add test.sh
> [root@localhost ~]# chkconfig test.sh on
> 
> # 5.关闭开机启动 
> [root@localhost ~]# chkconfig test.sh off
> 
> # 6.从chkconfig管理中删除test.sh
> [root@localhost ~]# chkconfig --del test.sh
> 
> # 7.查看chkconfig管理
> [root@localhost ~]# chkconfig --list test.sh
> ```
>
> 
>
> #### 自定义服务文件，添加到系统服务，通过Systemctl管理
>
> 1.写服务文件：如nginx.service、redis.service、supervisord.service
>
> ```bash
> [Unit]:服务的说明
> Description:描述服务
> After:描述服务类别
> 
> [Service]服务运行参数的设置
> Type=forking            是后台运行的形式
> ExecStart               为服务的具体运行命令
> ExecReload              为服务的重启命令
> ExecStop                为服务的停止命令
> PrivateTmp=True         表示给服务分配独立的临时空间
> 注意：启动、重启、停止命令全部要求使用绝对路径
> 
> [Install]               服务安装的相关设置，可设置为多用户
> WantedBy=multi-user.target 
> ```
>
> 
>
> 2.文件保存在目录下：以754的权限。目录路径：/usr/lib/systemd/system。如上面的supervisord.service文件放在这个目录下面。
>
> ```bash
> [root@localhost ~]# cat /usr/lib/systemd/system/nginx.service
> [root@localhost ~]# cat /usr/lib/systemd/system/supervisord.service
> ```
>
> 3.设置开机自启动(任意目录下执行)。如果执行启动命令报错，则执行：`sudo systemctl daemon-reload`
>
> ```bash
> 设置开机自启动
> [root@localhost ~]# systemctl enable nginx.service       
> [root@localhost ~]# systemctl enable supervisord
> 
> 停止开机自启动
> [root@localhost ~]# systemctl disable nginx.service
> [root@localhost ~]# systemctl disable supervisord
> 
> 验证一下是否为开机启动
> [root@localhost ~]# systemctl is-enabled nginx
> [root@localhost ~]# systemctl is-enabled supervisord
> ```
>
> #### 其他命令
>
> ```bash
> 启动nginx服务
> [root@localhost ~]# systemctl start nginx.service
> 
> 停止nginx服务
> [root@localhost ~]# systemctl start nginx.service
> 
> 重启nginx服务
> [root@localhost ~]# systemctl restart nginx.service
> 
> 查看nginx服务当前状态
> [root@localhost ~]# systemctl status nginx.service
> 
> 查看所有已启动的服务
> [root@localhost ~]# systemctl list-units --type=service
> ```
>
> 
>
> 5.服务文件示例：
>
> ```bash
> # supervisord.service进程管理服务文件
> [Unit]
> Description=Process Monitoring and Control Daemon   # 内容自己定义：Description=Supervisor daemon
> After=rc-local.service nss-user-lookup.target
> 
> [Service]
> Type=forking
> ExecStart=/usr/bin/supervisord -c /etc/supervisor/supervisord.conf
> ExecStop= /usr/bin/supervisorctl shutdown 
> ExecReload=/usr/bin/supervisorctl reload
> Restart=on-failure
> RestartSec=42s
> KillMode=process 
> 
> [Install]
> WantedBy=multi-user.target
> ```
>
> 
>
> ```bash
> # nginx.service服务文件
> [Unit]
> Description=nginx - high performance web server
> After=network.target remote-fs.target nss-lookup.target
> 
> [Service]
> Type=forking
> ExecStart=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
> ExecReload=/usr/local/nginx/sbin/nginx -s reload
> ExecStop=/usr/local/nginx/sbin/nginx -s stop
> 
> [Install]
> WantedBy=multi-user.target
> ```
>
> 使用Type=forking时，要求ExecStart启动的命令自身就是以daemon模式运行的。
> 而以daemon模式运行的进程都有一个特性：总是会有一个瞬间退出的中间父进程，
> 例如，nginx命令默认以daemon模式运行，所以可直接将其配置为forking类型
>
> ```bash
> # redis.service服务文件
> [Unit]
> Description=Redis
> After=network.target remote-fs.target nss-lookup.target
> 
> [Service]
> Type=forking
> ExecStart=/usr/local/bin/redis-server /etc/redis.conf
> ExecStop=kill -INT `cat /tmp/redis.pid`
> User=www
> Group=www
> 
> [Install]
> WantedBy=multi-user.target
> ```
>
> 



### 网上查到的资料：

[使脚本在网络启动后执行？](https://qastack.cn/unix/126009/cause-a-script-to-execute-after-networking-has-started)



### 开机启动执行脚本

#### 我是想开机的时候告诉我一下 IP 因为我ip不固定，没有显示器，也登不上路由器

目前我通知自己的方式是发邮件。使用的时163 邮箱，需要开一下 SMTP 服务

<img src="../../assets/image-20211124194504751.png" alt="image-20211124194504751" style="zoom:80%;" />

**！！！记好自己的授权码**



然后上代码：

```python
#/bin/env python
# -*-coding:utf8-*-
import socket
import time
import smtplib
import urllib.request
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText

# 第三方 SMTP 服务
mail_host = "smtp.163.com"         # SMTP服务器
mail_user = "xxx@163.com"          # 用户名
mail_pass = "xxx"                  # 授权密码，非登录密码

sender = "xxx@163.com"
receivers = ['xxx@163.com']

# 发送邮件的基本函数，参数依次如下
# smtp服务器地址、邮箱用户名，邮箱秘密，发件人地址，收件人地址（列表的方式），邮件主题，邮件html内容
def sendEmail(smtpserver,username,password,sender,receiver,subject,msghtml):
    msgRoot = MIMEMultipart('related')
    msgRoot["To"] = ','.join(receiver)
    msgRoot["From"] = sender
    msgRoot['Subject'] =  subject
    msgText = MIMEText(msghtml,'html','utf-8')
    msgRoot.attach(msgText)
    #sendEmail
    smtp = smtplib.SMTP()
    smtp.connect(smtpserver)
    smtp.login(username, password)
    smtp.sendmail(sender, receiver, msgRoot.as_string())
    smtp.quit()

# 检查网络
def check_network():
    while True:
        try:
            result=urllib.request.urlopen('http://baidu.com').read()
            print(result)
            print("Network is Ready!")
            break
        except Exception as e:
           print(e)
           print("Network is not ready,Sleep 5s....")
           time.sleep(5)
    return True

# 获得本机的ip地址
def get_ip_address():
    try:
        s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
        s.connect(("1.1.1.1",80))
        # s.connect(("8.8.8.8",80))
        ipaddr=s.getsockname()[0]
        print(ipaddr)
    finally:
        s.close()
    return ipaddr

if __name__ == '__main__':
    check_network()
    ipaddr=get_ip_address()
    sendEmail(mail_host,mail_user,mail_pass,sender,receivers,'IP Of Pi',ipaddr)
```



#### 1 脚本上，注意 Python3 的语法，否则可能报错哦

#### 2 获取本机 IP 的时候，connect(("1.1.1.1",80)) vs connect(("8.8.8.8",80))

可以看这篇文章[8.8.8.8 vs 1.1.1.1 – Which DNS is Better](https://turbospeedwifi.com/8-8-8-8-vs-1-1-1-1-which-dns-is-better/)

**1.1.1.1 is the better choice for most people. Cloudflare’s 1.1.1.1 public DNS service is generally the fastest to resolve domain names to IP addresses.** 