
# 安装

```sh
# 修改默认终端
chsh -s zsh 用户名
chsh -s tmux 用户名

# ssh 默认端口：8022
```

# 字体

[~/.termux/font.ttf](files/termux_font.ttf)

# 颜色配置

` ~/.termux/colors.properties `

```bash
# flat.colors
# Color scheme from https://github.com/Mayccoll/Gogh
color1=#0000ff
color3=#ffff00
color5=#00ffff
color6=#ff00ff
color7=#ffffff
color8=#f0f0f0
color11=#f0f0f0
# zsh .zip .tar.gz
color9=#a80909
# zsh username background, zsh current path foreground 
color0=#006054
# zsh current path background
color4=#cecb00
# exec
color10=#23fd00
# folder
color12=#cecb00
# bash command line folder path
color2=#23fd00
# bash command line "$"
color15=#23fd00
# properties value
color13=#00ff00
# properties key / note / ln
color14=#f6903b
background=#000000
#foreground=#d1ba74
#cursor=#1abc9c
cursor=#d1ba74
foreground=#00ffa8
```

# 界面配置

` ~/.termux/termux.properties `

```bash
extra-keys = [ \    
    [ESC, '[',  ']', '{', '}', '+', '=', '`', UP, BACKSLASH], \
    [TAB, CTRL, '"', '<', '>', {macro: '$PREFIX/', display:'根'}, '|', LEFT, DOWN, RIGHT] \
]
```

```bash
extra-keys = [ \
        [ESC, '[',  ']', '{', '}', '~/', '+', '=', UP, {macro: "CTRL b c", display: "[+]"}], \
        [TAB, CTRL, '"', '<', '>', {macro: '$PREFIX/', display:'根'}, '|', LEFT, DOWN, RIGHT] \
]
```