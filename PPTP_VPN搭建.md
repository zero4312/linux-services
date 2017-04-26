# Centos yum搭建PPTP VPN
### 1.安装
`yum install -y pptpd iptables`

### 2.配置
配置/etc/ppp/options.pptpd文件，找到网络和路由设置，我们设置ms-dns为8.8.8.8和8.8.4.4，编辑/etc/ppp/chap-secrets，按照格式来创建用户，设置密码。

```
# Secrets for authentication using CHAP
# client        server  secret                  IP addresses
  username      pptpd  Password                     *
  vultr         pptpd  vultrPassword                *
```

设置pptpd的守护进程，编辑`/etc/pptpd.conf`

```
localip   192.168.0.1 
remoteip  192.168.0.234-238  #分配给客户端的ip地址
```
编辑`/etc/sysctl.conf`文件，允许IP forwarding，添加`net.ipv4.ip_forward=1`到文件结尾

完事后 `sysctl -p`

配置iptables

```
iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 1723 -j ACCEPT
iptables -t nat -A POSTROUTING -o eth0 -s 192.168.0.0/24 -j MASQUERADE

```

上面 eth0 换成自己机器的外网网口 地址是前面编辑的分给客户段的ip段

重启后 iptables会失效 配置开机启动里。  

`chmod +x /etc/rc.d/rc.local`  
`vim /etc/rc.d/rc.local`

把配置写进去

启动pptpd服务，并设置为开机自动启动

`systemctl start pptpd.service`  
`systemctl enable pptpd.service`

至此 PPTP VPN服务搭建完毕。
