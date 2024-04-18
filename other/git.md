# 配置ssh代理

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

