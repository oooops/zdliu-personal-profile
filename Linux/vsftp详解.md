
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
 ######### 核心设置 ###########
 
 # 允许本地用户登录
 local_enable=YES
  
 # 本地用户的写权限
 write_enable=YES
  
 # 使用FTP的本地文件权限,默认为077
 # 一般设置为022
 local_umask=022
  
 # 切换目录时
 # 是否显示目录下.message的内容
 dirmessage_enable=YES
 dirlist_enable = NO
 #验证方式
 #pam_service_name=vsftpd
  
 # 启用FTP数据端口的数据连接
 connect_from_port_20=YES
  
 # 以独立的FTP服务运行
 listen=yes
  
 # 修改连接端口
 #listen_port=2121
  
 ######### 匿名登录设置 ###########
  
 # 允许匿名登录
 anonymous_enable=NO
  
 # 如果允许匿名登录
 # 是否开启匿名上传权限
 #anon_upload_enable=YES
  
 # 如果允许匿名登录
 # 是否允许匿名建立文件夹并在文件夹内上传文件
 #anon_mkdir_write_enable=YES
  
 # 如果允许匿名登录
 # 匿名帐号可以有删除的权限
 #anon_other_write_enable=yes
  
 # 如果允许匿名登录
 # 匿名的下载权限
 # 匿名为Other,可设置目录/文件属性控制
 #anon_world_readable_only=no
  
 # 如果允许匿名登录
 # 限制匿名用户传输速率,单位bite
 #anon_max_rate=30000
  
 ######### 用户限制设置 ###########
  
 #### 限制登录
  
 # 用userlist来限制用户访问
 #userlist_enable=yes
  
 # 名单中的人不允许访问
 #userlist_deny=no
  
 # 限制名单文件放置的路径
 #userlist_file=/etc/vsftpd/userlist_deny.chroot
  
 #### 限制目录
  
 # 限制所有用户都在家目录
 #chroot_local_user=yes
  
 # 调用限制在家目录的用户名单
 chroot_list_enable=YES
  
 # 限制在家目录的用户名单所在路径
 chroot_list_file=/etc/vsftpd/chroot_list
  
 ######### 日志设置 ###########
  
 # 日志文件路径设置
 xferlog_file=/var/log/vsftpd.log
  
 # 激活上传/下载的日志
 xferlog_enable=YES
  
 # 使用标准的日志格式
 #xferlog_std_format=YES
  
 ######### 安全设置 ###########
  
 # 用户空闲超时,单位秒
 #idle_session_timeout=600
  
 # 数据连接空闲超时,单位秒
 #data_connection_timeout=120
  
 # 将客户端空闲1分钟后断开
 #accept_timeout=60
  
 # 中断1分钟后重新连接
 #connect_timeout=60
  
 # 本地用户传输速率,单位bite
 #local_max_rate=50000
  
 # FTP的最大连接数
 #max_clients=200
  
 # 每IP的最大连接数
 #max_per_ip=5
  
 ######### 被动模式设置 ###########
  
 # 是否开户被动模式
 pasv_enable=yes
  
 # 被动模式最小端口
 pasv_min_port=5000
  
 # 被动模式最大端口
 pasv_max_port=6000
 

  
 ######### 其他设置 ###########
 # 欢迎信息
 ftpd_banner=Welcome to Ftp Server!
 
 #注意：这个地方如果不配置，就会出现只有root用户可以登陆，普通用户不可以
 check_shell=NO
 
 #注意：这个不配置有可能出现只能下载不能上传
 allow_writeable_chroot=YES
 
 #注意：该选项不配置可能导致莫名其妙的530问题
 seccomp_sandbox=NO
 
```

## ftp用户管理
 
　　# groupadd ftpUser  创建用户组

　　# useradd -d /u02/qsftp test  创建用户

　　# passwd test 设置密码
