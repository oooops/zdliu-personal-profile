
# vsftp详解

## 安装
 查看是否安装vsftp
``` js
rpm -qa | grep vsftpd
```
 安装vsftp
 ``` js
 // 如果带yum命令的很简单
  yum -y install vsftpd

 ```
 启动、关闭、重启：service vsftpd start/stop/restart.

 注：--查看vsftpd 启动状态

　　# chkconfig --list vsftpd

　　vsftpd          0:off   1:off   2:off   3:off   4:off   5:off   6:off

　　# chkconfig  vsftpd on

　　# chkconfig --list vsftpd

　　vsftpd          0:off   1:off   2:on    3:on    4:on    5:on    6:off

       这里看到，默认情况下从2到5设置为on了。2到5是多用户级别。 这个对应的是linux不同的运行级别。

我们也可以加level 选项来指定：

　　# chkconfig --level 0 vsftpd on   

　　# chkconfig --list vsftpd     

　　vsftpd          0:on    1:off   2:on    3:on    4:on    5:on    6:off

　　我们看到0已经设置为on了。

　　传统的init 定义了7个运行级（run level），每一个级别都代表系统应该补充运行的某些特定服务：

              （1）0级是完全关闭系统的级别

              （2）1级或者S级代表单用户模式

              （3）2-5 级 是多用户级别

              （4）6级 是 重新引导的级别
 


## 配置文件参数

#### 配置文件
* vsftpd.ftpusers：位于/etc目录下。它指定了哪些用户账户不能访问FTP服务器，例如root等。 
* vsftpd.user_list：位于/etc目录下。该文件里的用户账户在默认情况下也不能访问FTP服务器，仅当vsftpd .conf配置文件里启用userlist_enable=NO选项时才允许访问。 
* vsftpd.conf：位于/etc/vsftpd目录下。来自定义用户登录控制、用户权限控制、超时设置、服务器功能选项、服务器性能选项、服务器响应消息等FTP服务器的配置。

#### vsftpd.conf
```html
#禁用匿名用户登陆
anonymous_enable=NO

#允许本地用户登陆
local_enable=YES

#允许本地用户写入
write_enable=YES

#注意：这个地方如果不配置，就会出现只有root用户可以登陆，普通用户不可以
check_shell=NO

#掩码，决定了上传上来的文件的权限。设置为000使之有最大权限
local_umask=000

#允许记录日志
xferlog_enable=YES

#允许数据流从20端口传输
connect_from_port_20=YES

#日志路径
xferlog_file=/var/log/vsftpd.log

#ftp欢迎语，可以随便设置
ftpd_banner=hi,guys!

#注意：这个选项可以保证用户锁定在指定的家目录里，防止系统文件被修改。
chroot_local_user=YES

#注意：这个不配置有可能出现只能下载不能上传
allow_writeable_chroot=YES

#配置了可以以stand alone模式运行
listen=YES

#注意：该选项不配置可能导致莫名其妙的530问题
seccomp_sandbox=NO

#说明我们要指定一个userlist，里边放的是允许ftp登陆的本地用户。如果设置为YES，则文件里设置的是不允许登陆的本地用户
userlist_deny=NO
userlist_enable=YES

#记录允许本地登陆用户名的文件
userlist_file=/etc/vsftpd/allowed_users
```

## ftp用户管理
 
　　# groupadd ftpUser  创建用户组

　　# useradd -d /u02/qsftp test  创建用户

　　# passwd test 设置密码
