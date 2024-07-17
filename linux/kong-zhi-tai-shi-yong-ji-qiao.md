# 控制台使用技巧

### 操作快捷键

```
Ctrl + r
# 快速查找历史命令

Ctrl +  l
# 清理控制台屏幕

Ctrl + a \ Ctrl + e
# 移动光标至命令行首 \ 行尾

Ctrl + w \ Ctrl + k
# 删除光标之前 \ 之后的内容
```

####

### VIM文件编辑快捷键

```
快捷键ZZ
# 文件保存并退出
```

#### vim 配置

```bash
vim ~/.vimrc
set ignorecase
set autoindent
set tabstop=2
set shiftwidth=2
set expandtab
set softtabstop=2

autocmd BufNewFile *.py,*.cc,*.sh,*.java exec ":call SetTitle()"

func SetTitle()
  if expand("%:e") == 'sh'
    call setline(1,"#!/bin/bash")
    call setline(2, "# ------------------------------------------------------------")
    call setline(3, "# File Name: ".expand("%"))
    call setline(4, "# Version: v1.0")
    call setline(5, "# Author: Will")
    call setline(6, "# Created Time : ".strftime("%F %T"))
    call setline(7, "# www.yunwei361.com")
    call setline(8, "# ------------------------------------------------------------")
    call setline(9, "")
  endif
endfunc
```

###

### 进程操作快捷键

```
Ctrl + c
# 强制终止程序的执行

Ctrl + z
# 挂起一个进程

Ctrl + d
# 终端中输入 exit 后回车
```

####

### Linux命令中快捷键（top）

```
Shift + p
# 根据 CPU 使用率排序

Shift + m
# 根据内存占用排序
```
