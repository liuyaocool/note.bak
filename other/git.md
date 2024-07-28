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


# 常用命令

```bash
# 检出远程分支到本地
git checkout -b <本地分支名> <远程分支名>

# ??
git push <remote_name> <local_branch_name>:<remote_branch_name>

# 撤销最后一次提交 且删除本地修改
git reset --hard HEAD~1

# 撤销最后一次提交 但保留本地修改
git reset --soft HEAD~1

```