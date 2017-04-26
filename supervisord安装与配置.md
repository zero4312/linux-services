##Supervisord 安装与配置

###安装
其实官网上提供了完整的，多样的安装方式  
我这里选择pip安装  
`pip install supervisor`  
系统中如果没有pip命令的话，用yum安装pip  
`yum install python-pip`  

###配置
centos7通过pip安装完后，默认配置会生成在`/etc/supervisord.conf`  
打开编辑该文件，在结尾处添加需要的配置和需要监护的进程或脚本  
贴个监护shadowsocks的配置

```
[program:shadowsocks_jp]
command=/opt/go/bin/shadowsocks-local -c /opt/ss/jp.json
autostart=true
autorestart=true
user=root
```

需要注意的地方  
supervosrd配置文件：
command 字段设置的是后台守护应用的启动命令, 注意: 该命令必须是在前台执行的, 即会独占控制台, 否则会导致 supervisor 无法获得标准输出, 并失去进程的控制权.

supervisord是否以daemon方式运行，是在supervisord.conf的supervisord项里配置的，当配置了nodaemon=true时，就是以非daemon方式运行。与它是否带参数运行没有关系。

它监控的进程是否随着supervisord被杀而退出，跟supervisord是否以daemon方式运行没有关系。当你使用kill -9关闭supervisord时，supervisord就会来不及告诉它监控的进程，它已经关闭了，它监控的进程就变成没人管理的进程。当使用kill -15杀死supervisord时，supervisord有足够的时间来处理它监控的进程，它监控的进程会跟着supervisord一起关闭。

###运行和管理

supervisord (以daemon方式启动)  
或  
supervisord -c /etc/supervisord.conf 

supervisord，初始启动Supervisord，启动、管理配置中设置的进程。  

supervisorctl 简单的后台进程控制工具  
supervisorctl stop programxxx，停止某一个进程(programxxx)，programxxx为[program:chatdemon]里配置的值  
supervisorctl start programxxx，启动某个进程  
supervisorctl restart programxxx，重启某个进程  
supervisorctl stop all，停止全部进程，注：start、restart、stop都不会载入最新的配置文件。  
supervisorctl reload，载入最新的配置文件，并按新的配置启动、管理所有进程。  
supervisorctl reread，当一个服务由自动启动修改为手动启动时执行一下就ok  