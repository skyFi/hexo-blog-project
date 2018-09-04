---
title: vim 编辑器快捷键
date: 2018-09-04 20:46:22
tags: 工具
---

## 一、vi/vim 配置文件
系统的vim配置文件为/etc/vimrc文件，用户级别的配置文件在~/.vimrc文件中，如果没有.vimrc文件，则先创建.vimrc文件
```
touch ~/.vimrc
```
vimrc部分常用配置如下：
```
set sw=4 "自动缩进时，缩进尺寸为4个空格
set ts=4 "tab宽度为4个字符
set et "编辑时将所有tab替换为空格
set smarttab "删除时，一个删除键删除4个空格
set selection=inclusive "指定在选择文本时，光标所在位置也属于被选中范围
set autoindent "设置自动缩进

set number "显示行号
set ru "显示vim状态栏，显示行号、列号
set hls "搜索时高亮显示被找到的文本

set encoding=utf-8 "设置当前字符编码为UTF-8
set fileencodings=ucs-bom,utf-8,cp936,gb18030,big5,euc-jp,euc-kr,latin1 "如果文件编码与vim编码不一致，按顺序尝试解码，如果成功则将fileencoding设置为这个值，在文件保存/打开时，自动对文件编解码转换

syntax on "打开关键字上色
filetype plugin indent on "启动文件类型自动识别、文件类型相关插件、按文件类型的自动缩进
```
## 二、vi/vim 常用快捷键
vi有三种模式：插入模式、命令模式、底行模式

* 插入模式：可插入字符，esc退出
* 命令模式：可进行移动光标，删除字符，复制字符等操作
* 底行模式：顾名思义，可在末行进行文本匹配、跳转、文本替换等操作

### 命令：

* `:n`	跳转到第n行
* `:x`	保存并退出
* `:wq`	保存并退出
* `:w`	保存
* `:q`	退出编辑器
* `:q!`	退出编辑器，不保存
* `ctrl + r`	恢复上一步被撤销的操作
* `shift + 方向键`	按指定方向跳一个单词
* `a`	在光标右侧添加文本
* `i`	在光标左侧添加文本
* `A`	在行末添加文本
* `I`	在行首添加文本
* `O`	在当前行上添加新行
* `o`	在当前行下添加新行
* `R`	替换编辑光标及后面的文本
* `J`	合并当前行及下一行为一行
* `h,j,k,l`	左、下、上、右
* `空格,backspace,enter,-`	右、左、下一行行首、上一行行首
* `/xxx`	向下搜索字符串xxx
* `?xxx`	向上搜索字符串xxx
* `n`	向下搜索前一个搜索动作
* `N`	向上搜索前一个搜索动作
* `x`	删除当前字符
* `nx`	删除光标开始的n字符
* `dd`	删除行
* `ndd`	向下删除当前行在内的n行
* `u`	撤销上一步操作
* `U`	撤销当前行的所有操作
* `n+`	向下跳n行
* `n-`	向上跳n行
* `nG`	跳转到行号为n的行
* `G`	跳转到文件底
* `:set number`	显示行号
* `:set nonumber`	不显示行号
* `yy`	复制当前行到缓存区
* `nyy`	复制当前行及向下n行到缓存区
* `yw`	复制光标开始到词尾的字符
* `nyw`	复制光标开始的n个单词
* `y^`	复制光标到行首的内容
* `y$`	复制光标到行尾的内容
* `p`	在光标后粘贴缓存区的内容
* `P`	在光标前粘贴缓存区的内容
* `:s/old/new`	用new替换当前行中首次出现的old
* `:s/old/new/g`	用new替换当前行中所有的old
* `:n,m s/old/new/g`	用new替换n到m行中所有的old
* `:%s/old/new/g`	用new替换当前文件中所有的old
* `:filetype`	查看vim文件类型检测功能是否开启
* `:set filetype=java`	将当前文件类型设置为java，将会按照java文件进行文本高亮
* `ctrl + n/p`	代码补全［插入模式］
