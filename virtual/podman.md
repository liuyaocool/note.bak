
# 配置文件

https://docs.podman.io/en/latest/markdown/podman-search.1.html

- `/etc/containers/registries.conf`
- `~/.config/containers/registries.conf`

**这个可用**

```
unqualified-search-registries = ["docker.io"] 

[[registry]] 
prefix = "docker.io" 
location="1dr5t5mc.mirror.aliyuncs.com"
```

**这个报错 ??**
```
unqualified-search-registries = ['aliyuncs.com', 'docker.io', 'quay.io', 'registry.fedoraproject.org']

# 阿里云加速镜像站
[[registry]]
prefix="aliyuncs.com"
location="xxx.mirror.aliyuncs.com/library"

[[registry]]
prefix="docker.io"
location="mirror.gcr.io"

[[registry]]
prefix="docker.io/library"
location="docker.io/library"
# 原版配置 但无法拉取镜像
#location="quay.io/libpod" 

[[registry]]
location="localhost:5000"
insecure=true
```


# 相关脚本

## 搜索

```bash
#!/bin/bash

if [ $# -eq 0 ]; then
    echo "Usage: $0 <name> <params not format>"
    echo "Such as: $0 nginx --limit 10"
    exit 1
fi

# 获取终端的宽度
terminal_width=$(tput cols)
# 计算第一列的宽度
column1_width=$(( terminal_width - 30 ))  # 假设后两列的宽度为 30

printf "%-${column1_width}s %-10s %-10s\n" "Name" "Stars" "Official"
printf "%*s\n" $terminal_width | tr ' ' '-'

# $@ 获得所有参数
podman search $@ --filter stars=1 --format "{{.Name}}\t{{.Stars}}\t{{.Official}}"  | \
awk -v col1_width="$column1_width" 'BEGIN { FS="\t" } {printf "%-*s %-10s %-10s\n", col1_width, $1, $2, $3}' | \
sort -k2rn
```