
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

**这个报错**
```
unqualified-search-registries = ['aliyuncs.com', 'docker.io', 'quay.io', 'registry.fedoraproject.org']

# 阿里云加速镜像站
[[registry]]
prefix="aliyuncs.com"
location="xxx.mirror.aliyuncs.com"

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