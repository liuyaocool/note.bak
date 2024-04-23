# ssh

## 免密登录

### 服务端

**修改配置**
```bash
sudo vi /etc/ssh/sshd_config

    # ----- 修改 -------
    RSAAuthentication yes
    PubkeyAuthentication yes
    # 密码不能为空
    PermitEmptyPasswords no
    #AuthorizedKeysFile .ssh/authorized_keys
    # 关闭密码登录 建议关闭
    PasswordAuthentication no
    # 是否允许root用户通过SSH登录
    PermitRootLogin yes

# 重启 ssh
service sshd restart
```

**创建文件**

```bash
# 如果没有 .ssh 路径
mkdir -p ~/.ssh
chmod 700 ~/.ssh

touch ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

### 客户端

```bash
# 1. 生成密钥
ssh-keygen -t ed25519
# 2. 公钥上传至服务器
# ~/.ssh/id_ed25519.pub 内文本复制到服务端~/.ssh/authorized_keys文件内
# or 
ssh-copy-id -p {port} {remote user}@{remote host}

# 3. 指定私钥文件登录
ssh -p {port} -i "/home/{user}/.ssh/id_ed25519" {remote user}@{remote host}
```

**登录报错**

- 报错1：ERROR: Host key verification failed. 解决：vi ~/.ssh/known_hosts ，删除某一行

# 键盘

## 设置默认为F1~F12

描述：顶部一排功能键区 (F1~F12) 的默认行为控制屏幕亮度、音量等功能键，F1~F12键需要 Fn 键一起才能触发。

```bash
# 0  禁用功能键，按 ‘Fn’ + ‘F8’ 等同于 F8
# 1  默认功能键，按 ‘F8’ 触发功能键 (play/pause)，按 ‘Fn’ + ‘F8’ 触发 F8 键
# 2  默认非功能键，按 ‘F8’ 触发 F8 键，按 ‘Fn’ + ‘F8’ 触发功能键 (play/pause)

# 方案1: 重启失效
echo 2 > /sys/module/hid_apple/parameters/fnmode


# 方案2: 永久生效
vi /etc/modprobe.d/hid_apple.conf
## ---------- 修改或添加 ----------
options hid_apple fnmode=2
## ---------- end ----------
```
