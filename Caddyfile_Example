>版权声明：本文为CSDN博主「kjh2007abc」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/kjh2007abc/article/details/85001330

# 单一静态站点
Caddyfile样例如下：
```
test.ptmind.com:80{
    #监听80端口，写443 端口，会要求输入邮箱地址，自动生成ssl 加密证书
    gzip                   #开启gzip 
    log  /var/log/caddy/test.ptmind.com.log         #访问日志输出位置
    errors  /var/log/caddy/test.ptmind.com.log         #错误日志输出位置
    root /etc/caddy/web-test     #站点家目录
    index index.html index.htm   #index 文件在在顺序
}
```

# 单一静态站点，自动生成ssl证书
```
test.ptmind.com:443{
    #写443 端口，会要求输入邮箱地址，自动生成ssl 加密证书
    gzip                  
    log  /var/log/caddy/test.ptmind.com.log  
    root /etc/caddy/web-test  
    index index.html index.htm
}
```
## 生成的证书存放位：
启动caddy 系统用户家目录下面：
我使用root用户启动的
```
[root@hkjump caddy]# ls -all /root/.caddy/total 16drwx------  4 root root 4096 Apr 26 20:49 .
dr-xr-x---. 5 root root 4096 Apr 28 17:02 ..
drwx------  3 root root 4096 Apr 26 20:49 acme
drwx------  2 root root 4096 Apr 26 20:49 ocsp
```

# virtualhost支持配置，一个端口支持监听多个域名：
```
https://t1.ptmind.com{
    gzip
    log  /var/log/caddy/t1.ptmind.com.log
    root /etc/caddy/web-test1  
    index index.html index.htm
}
https://t2.ptmind.com{ 
    gzip                  
    log  /var/log/caddy/t2.ptmind.com.log  
    root /etc/caddy/web-test2  
    index index.html index.htm
}
```

# 目录文件列表浏览
```
https://caddy.ptbox.cn {
    root /tmp/ #浏览家目录 
    browse #开启目录浏览 
}
```

# fastcgi代理
caddy 可以将请求通过fastcgi接口发送给后端的实现fastcgi的server。
安装启动服务
`/etc/init.d/php-fpm start`

查看服务状态
```
[root@hkjump php-fpm.d]# /etc/init.d/php-fpm status
php-fpm (pid 16137) is running...
```
验证服务端口
```
[root@hkjump php-fpm.d]# lsof -n -i:9000COMMAND   PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
php-fpm 16137 root    7u  IPv4  95128      0t0  TCP 127.0.0.1:cslistener (LISTEN)
php-fpm 16138  www    0u  IPv4  95128      0t0  TCP 127.0.0.1:cslistener (LISTEN)
php-fpm 16139  www    0u  IPv4  95128      0t0  TCP 127.0.0.1:cslistener (LISTEN)
php-fpm 16140  www    0u  IPv4  95128      0t0  TCP 127.0.0.1:cslistener (LISTEN)
php-fpm 16141  www    0u  IPv4  95128      0t0  TCP 127.0.0.1:cslistener (LISTEN)
php-fpm 16142  www    0u  IPv4  95128      0t0  TCP 127.0.0.1:cslistener (LISTEN)
```
caddy 配置如下：
```
https://caddy.ptbox.cn {
    gzip
    fastcgi / 127.0.0.1:9000 php                  
    log  /var/log/caddy/caddy.ptbox.cn.log 
    root /etc/caddy/php 
}
```
创建index.php 文件：
```
[root@hkjump caddy]# more /etc/caddy/php/index.php
<?php phpinfo(); ?>
```
验证，浏览器访问 caddy.ptbox.cn，即可看到 php 相关配置信息；

# 跳转功能: redir
## 域名跳转
test.ptbox.cn 直接跳转到 www.ptmind.com 域名
```
test.ptbox.cn:443 {
    tls test@ptbox.cn
    redir https://www.ptmind.com{uri}
}
## 文件跳转
`redir /resources/images/photo.jpg /resources/images/drawing.jpg 307`

## 多目录跳转
```
redir 307 {  /foo     /info/foo
    /todo    /notes
    /api-dev /api       
    meta
}
```
## 根据协议跳转：
所有http 请求跳转到一个域名
```
redir 301 {
  if {>X-Forwarded-Proto} is http
  /  https://{host}{uri}
}
```

# http.git 结合git push自动部署站点或者代码
## 配置选项含义
```
git [repo path] {
  repo        :支持SSH和HTTPS URL。
  path        :存储clone代码存储目标路径; 默认是站点 根目录。它可以是绝对的或相对的；
  branch      ：git代码分支或标签; 默认是master分支。
  key         ：SSH私钥的路径
  interval    ：拉取代码之间的时间间隔; 默认为3600（1小时），最小为5.间隔-1禁用周期性拉。  clone_args  ：额外的cli args传递给git cloneeg --depth=1。git clone当第一次获取源时调用。
  pull_args   ：额外的cli args传递给git pulleg  -s recursive -X theirs。git pull当源被更新时使用。
  hook        ：hook路径和密码用于创建一个webhook，目前仅支持 GitHub和Travis 支持秘密。
  hook_type   ：Webhook类型是自动检测的，GitHub, Gitlab, BitBucket, Travis and Gogs。  then  command [args...] :clone成功后执行的命令;  then_long command [args...] :用于长时间执行的命令，应该在后台运行。
}
```
## 定时git clone 自动拉取代码
```
https://caddy.ptbox.cn {
    gzip
    log  /var/log/caddy/caddy.ptbox.cn.log
    root /etc/caddy/web-test
    index index.html
    git   git@gitlab.ptmind.com:kevin/web-test.git /etc/caddy/web-test {
        branch v2       #git代码分支或标签;    
        interval 6000   #拉取代码之间的时间间隔;    
        key  /root/.ssh/id_rsa
    }
}
```
## 设置web hook ，根据git commit 状态自动拉取更新代码
注意：在gitlab 项目上配置web-hook 地址为：https://caddy.ptbox.cn/webhook
```
https://caddy.ptbox.cn {
    gzip
    log  /var/log/caddy/caddy.ptbox.cn.log
    root /etc/caddy/web-test
    index index.html
    git   git@gitlab.ptmind.com:kevin/web-test.git /etc/caddy/web-test {
        branch v2    
        hook   /webhook    #设置hook地址，    
        hook_type gitlab   #hook 类型    
        key  /root/.ssh/id_rsa
        then chmod 755 /var/log/caddy/caddy.ptbox.cn.log
    }
}
```
当git 更新后，会通知caddy 服务，caddy 日志会提示一下信息：
```
2017/05/02 15:50:02 Received pull notification for the tracking branch, updating...
From gitlab.ptmind.com:kevin/web-test
 * branch            v2         -> FETCH_HEAD
```

# 负载均衡
Caddy支持负载均衡配置，并支持三种负载均衡算法：random（随机）、least_conn（最少连接）以及round_robin(轮询调度)。
负载均衡同样是通过proxy middleware实现的。
```
localhost:2015 { 
log ./2015.log

proxy / localhost:9001 localhost:9003 { 
policy round_robin 
} 
proxy /bar localhost:9002 localhost:9004 { 
policy least_conn 
} 
}
```
