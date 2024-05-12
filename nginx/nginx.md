# 源码安装

## 1. 下载 解压: nginx.org下载, 解压源码：tar -zxvf nginx*.tar

## 2. 配置configure:

cd 源码目录执行 

`../configure --prefix=/home/xxx/bin/nginx --with-http_ssl_module --with-http_v2_module --with-http_stub_status_module --with-http_mp4_module --add-module=../modules/headers-more-nginx-module`

- `--prefix=/home/xxx/bin/nginx`: 指定安装路径
- `--with-http_stub_status_module` ：上报自身状态 如k8s管理nginx
- `--with-http_ssl_module` ：ssl模块 需要上一模块
- `--with-http_mp4_module` ：视频分段观看
- `--add-module=../modules/headers-more-nginx-module`: 可修改响应头
- `nginx -V` : 可查看安装的模块

## 3. configure报错

1、 C compiler cc is not found:

- `apt-get install build-essential`

2、 pcre: 

```bash
./configure: error: the HTTP rewrite module requires the PCRE library.
You can either disable the module by using --without-http_rewrite_module
option, or install the PCRE library into the system, or build the PCRE library
statically from the source with nginx by using --with-pcre=<path> option.
```
- centos： `yum install -y pcre pcre-devel`
- ubuntu： `apt-get install libpcre3 libpcre3-dev -y`
- macos： `brew install pcre`

3、 openssl

```bash
./configure: error: SSL modules require the OpenSSL library.
You can either do not enable the modules, or install the OpenSSL library
into the system, or build the OpenSSL library statically from the source
with nginx by using --with-openssl=<path> option.
```
- ununtu： `sudo apt-get install openssl libssl-dev -y`
- macos??： `sbrew search openssl` --查看搜索到的--> `brew install openssl@3`

4、 zlib
    
`./configure: error: the HTTP gzip module requires the zlib library.`
 
- centos： `yum install -y zlib zlib-devel`
- ubuntu： `apt-get install zlib1g-dev -y`

## 4. 编译 安装

1. 执行 `make`
2. 执行 `make install`
3. 普通用户加执行权限: `chmod u+s ./nginx`
4. 普通用户使用80端口: `doas setcap cap_net_bind_service=+eip xxx/sbin/nginx`

## 5. 启动 访问

- 启动: `nginx`
- 停止: `nginx -s stop`
- 配置重载: `nginx -s reload`
- 指定配置: `nginx -c nginx.conf`

访问不通, 检查防火墙 网络配置, 关闭防火墙

```bash
# 关闭防火墙
systemctl stop firewalld.service
# 禁止开机启动防火墙
systemctl disable firewalld.service
```

## 6. 添加模块

```bash
# 1.检查已安装模块
➜  nginx-1.24.0: nginx -V     
nginx version: nginx/1.24.0
built by gcc 11.4.0 (Ubuntu 11.4.0-1ubuntu1~22.04) 
built with OpenSSL 3.0.2 15 Mar 2022
TLS SNI support enabled
configure arguments: --prefix=/root/nginx/server --with-http_ssl_module --with-http_stub_status_modul
# 2. 下载模块 , 例如ngx_http_headers_more_module模块
git clone https://github.com/openresty/headers-more-nginx-module.git ~/soft/nginx/modules/headers-more-nginx-module
# 3. 根据1 中的configure arguments 追加命令
./configure --prefix=/root/nginx/server --with-http_ssl_module --with-http_stub_status_modul --add-module=~/soft/nginx/modules/headers-more-nginx-module
# 4. 编译 && 安装
make && make install
```

# nginx.conf

## 基础配置

```nginx
{
    user	root;
    worker_processes	1; # 进程个数
    #error_log  logs/error.log;
    #error_log  logs/error.log  notice;
    #error_log  logs/error.log  info;
    #pid        logs/nginx.pid;
    events {
        worker_connections  1024;
    }

    http {
        include       mime.types;
        default_type  application/octet-stream;
        #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
        #                  '$status $body_bytes_sent "$http_referer" '
        #                  '"$http_user_agent" "$http_x_forwarded_for"';
        
        #access_log  logs/access.log  main;
        sendfile        on;
        #tcp_nopush     on;	
        keepalive_timeout  60; # 超时时间, 设置为0 一直等待
        #gzip  on;    
        server {
        }
    }
}
```

## server

```nginx
server {
	listen                          443 ssl;
	server_name                     xxx.com;
	ssl_certificate                 ssl/xxx.com.pem;
	ssl_certificate_key             ssl/xxx.com.key;
	ssl_session_timeout             5m;
	ssl_session_cache               shared:SSL:1m;
	ssl_protocols                   TLSv1.1 TLSv1.2 TLSv1.3;
	ssl_ciphers                     ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HI
GH:!NULL:!aNULL:!MD5:!ADH:!RC4;
	ssl_prefer_server_ciphers       on;
	client_max_body_size            100M; # request最大尺寸

	location / {

	}
}
```

## location

```nginx
location / {
	proxy_pass   https://gitee.com/;
	proxy_set_header 'Referer' ''; # modify request header
}
```

todo 。。。

# 示例

## 1. 代理gitee原始文件 可做图床

需要添加 `headers-more-nginx-module` 模块

```nginx
{
    user  root;
    worker_processes  1;
    events {
        worker_connections  1024;
    }
    http {
        include       mime.types;
        default_type  application/octet-stream;
        sendfile        on;
        keepalive_timeout  65;
        server {
            listen                      80;
            server_name                 gitee.proxy.xxx.com;
            error_page  500 502 503 504  /50x.html;

            location / {
                proxy_pass   https://gitee.com/;
                # modify request header
                proxy_set_header 'Referer' '';
                proxy_set_header 'Accept' 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7';
                proxy_set_header 'Host' 'gitee.com';
                proxy_set_header 'Sec-Fetch-Dest' 'document';
                proxy_set_header 'Sec-Fetch-Mode' 'navigate';
                proxy_set_header 'Sec-Fetch-Site' 'none';
                proxy_set_header 'Sec-Fetch-User' '?1';
                # modify response header
                # more_set_headers 'Content-Type: text/css';
            }
        }
    }
}
```