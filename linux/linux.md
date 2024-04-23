
# 常用命令

## 烧录

`dd if=/home/xxx.iso of=/dev/sdc status=progress`

## 监控

- 磁盘: `iostat`(sysstat), `iotop`
- cpu、内存: `htop`
- 系统基础信息: `neofetch`

# 防火墙

## firewall

- `firewall-cmd --zone=public --list-ports` 查看开放的端口列表           
- `firewall-cmd --query-port=3306/tcp` 查看防火墙某个端口是否开放           
- `firewall-cmd --zone=public --add-port=3306/tcp --permanent` 开放端口            
- `firewall-cmd --zone=public --add-port=40000-45000/tcp --permanent` 开放一段端口           
- `firewall-cmd --reload` 重启防火墙
- `systemctl start/stop/status firewalld` 防火墙操作

