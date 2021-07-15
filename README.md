Caddy安装
[官网](https://caddyserver.com/)
# 安装Centos7.5
```
yum install yum-plugin-copr
yum copr enable @caddy/caddy
yum install caddy
```
# 服务文件
```
# caddy.service
[Unit]
Description=Caddy
After=network.target network-online.target
Requires=network-online.target

[Service]
Type=notify
ExecStart=/usr/bin/caddy run --environ --config /etc/caddy/Caddyfile
ExecReload=/usr/bin/caddy reload --config /etc/caddy/Caddyfile
TimeoutStopSec=5s
LimitNOFILE=1048576
LimitNPROC=512
PrivateTmp=true
ProtectSystem=full
AmbientCapabilities=CAP_NET_BIND_SERVICE

[Install]
WantedBy=multi-user.target
```

# 配置文件,各配置案例见其它
```
config/Caddyconfig

```

