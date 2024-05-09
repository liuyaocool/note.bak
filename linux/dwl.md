# 依赖

若编译失败 检查依赖是否安装

- wlroots
- wayland-protocols

# 启动脚本

```bash
#!/bin/bash

if test -z "${XDG_RUNTIME_DIR}"; then
    export XDG_RUNTIME_DIR=/tmp/run/user/${UID}
    mkdir -p ${XDG_RUNTIME_DIR}
    chmod 0700 "${XDG_RUNTIME_DIR}"
fi

# export DISPLAY=:0
# input method
export XMODIFIERS=@im=fcitx
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export SDL_IM_MODULE=fcitx
# obs studio
export QT_QPA_PLATFORM=wayland
# code env
export PATH=$PATH:~/bin/wayland

# ssh login on out network
# autossh -M 4000 -NR 8822:127.0.0.1:22 root@autossh.liuyao.link -p 27085

# ----------------- run dwl -----------------------
dbus-launch --exit-with-session dwl -s \
"sleep 1 &&
waybarc
" &>> /tmp/dwl-console.log

# "  2 >& 1 >> /tmp/dwl-console.log
```

# 状态栏 waybar

## 启动脚本

```bash
#!/bin/bash
cp=${HOME}/.config/waybar
lib_p=${HOME}/bin/wayland/waybar-lib
LD_LIBRARY_PATH==${HOME}/bin/wayland/waybar-lib
exe_p=${HOME}/bin/wayland/waybar
if [ -n "$1" ] ;then
	cfg_p=`find ${cp} -type d -name "${1}*" | head -n 1`
	LD_LIBRARY_PATH=${lib_p} ${exe_p} -c ${cfg_p}/config -s ${cfg_p}/style.css
	# LD_LIBRARY_PATH=${lib_p} ${exe_p} -c ${cp}/${1}-*/config.json -s ${cp}/${1}-*/style.css
else
	LD_LIBRARY_PATH=${lib_p} ${exe_p} &
fi
```