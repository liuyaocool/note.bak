# 配置ssh代理

` vi ~/.ssh/config`

```
Host qqvps
    HostName qqvps.xxx.xxx
    Port 22
    User root
    DynamicForward 1080
Host github.com
  HostName github.com
  User git
  ProxyCommand nc -x 127.0.0.1:1080 %h %p
```

> 1080 为本地端口
>
> 此配置添加完成后 需要执行 `	ssh -N qqvps  ` 才能连接远程， 从而实现外网搭桥
