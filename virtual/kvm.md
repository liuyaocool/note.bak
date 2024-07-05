# QEMU/KVM 虚拟机

## archlinux安装

```bash
sudo pacman -S libvirt 
sudo pacman -S qemu-base
# 可视化
sudo pacman -S virt-manager

# 添加至用户组 libvirt
sudo usermod -aG libvirt $USER

# 重启防火墙和libvirt
sudo systemctl restart firewalld 
sudo systemctl restart libvirtd
```

## 添加虚拟机

打开virt-manager -> 文件 -> 添加连接 -> 选QEMU/KVM -> 点“连接”

## 驱动程序

- [win虚拟机驱动(spice-guest 从gitee)](https://gitee.com/liuyao_cool/linux-wm/raw/master/doc/spice-guest-tools-latest.exe)
- [win虚拟机驱动(spice-guest)](files/spice-guest.exe)

## 一些错误

1. 不支持qxl
   - 安装 `qemu-hw-display-qxl`  
2. unknown audio driver 'spice'
   - 安装 `qemu-audio-spice `
3. -chardev spicevmc,id=charchannel0,name=vdagent:'spicevmc' is not a valid char driver name
   - 安装 `qemu-chardev-spice`
