

# zsh

## 配置文件 

`vi .zshrc`

```bash
# 关闭自动更新
zstyle ':omz:update' mode disabled
DISABLE_MAGIC_FUNCTIONS=true

export ZSH="$HOME/.oh-my-zsh"

ZSH_THEME="agnoster"

plugins=(git zsh-autosuggestions)

source $ZSH/oh-my-zsh.sh
```

## 美化 ohmyzsh


```bash
git clone https://github.com/ohmyzsh/ohmyzsh.git
# 执行
./tools/install.sh
# 安装路径 ~/.oh-my-zsh/

# 修改主题
vi ~/.zshrc
## ---------- 修改 ----------
ZSH_THEME="agnoster"
## ---------- end ----------

```
**修改主题：agoster**

```bash
vi ~/.oh-my-zsh/themes/agnoster.zsh-theme
## ------------- 修改 ------
# Context: user@hostname (who am I and where am I)
prompt_context() {
  if [[ "$USERNAME" != "$DEFAULT_USER" || -n "$SSH_CLIENT" ]]; then
    # xxx 修改显示格式
  fi
}
```

**修改主题：rkj-repos**

```bash
vi ~/.oh-my-zsh/themes/rkj-repos.zsh-theme
## ------------ 修改 ---------
PROMPT=$'%{$fg_bold[blue]%}┌─[%{$fg_bold[green]%}%n%b %b%{$fg[yellow]%}'%D{"%m/%d %I:%M:%S"}%b$'%{$fg_bold[blue]%}]%{$reset_color%} - %{$fg_bold[blue]%}[%{$fg_bold[default]%}%~%{$fg_bold[blue]%}]
%{$fg_bold[blue]%}└─[%{$fg_bold[magenta]%}%?$(retcode)%{$fg_bold[blue]%}] <$(mygit)$(hg_prompt_info)>%{$reset_color%} '
## ------------- end ---------
```

**自动转义问题**

```bash
cat ~/.oh-my-zsh/lib/misc.zsh
# 有一行 if [[ $DISABLE_MAGIC_FUNCTIONS != true ]]; then

vi ~/.zshrc
    ## ------ 在 source 前加一行 -------
    DISABLE_MAGIC_FUNCTIONS=true
    source $ZSH/oh-my-zsh.sh
    ## -------- end ------------------
```

## 插件1: 自动补全插件

```bash
# https://github.com/zsh-users/zsh-autosuggestions/blob/master/INSTALL.md#oh-my-zsh
vi ~/.zshrc
    # 添加变量
    export TERM=st-256color
    # 修改 
    plugins=(
        git
        zsh-autosuggestions
    )
```

# ranger 文件浏览器

# Vim

## 配置

`vim ~/.vimrc`

```bash
set mouse=          " 关闭鼠标指针
syntax on           " 语法高亮
set tabstop=4       " 设置制表符宽度为 4
set shiftwidth=4    " 设置自动缩进宽度为 4
" set expandtab       " 将制表符扩展为空格
```

## 格式化代码

1. 下载 `https://gitee.com/liuyao_cool/vim-autoformat/tree/master/plugin` 下的文件
2. 将 `*.vim` 复制到 `~/.vim/plugin` 下
3. 重新 `vim` 一个文件, 输入 `:Autoformat` 即可格式化代码
