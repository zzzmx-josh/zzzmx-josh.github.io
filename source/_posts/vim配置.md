---
title: vim配置
date: 2023-05-04 09:19:07
tags: linux 
---

# 介绍

Linux最早的编辑器为vi，Visual interface的缩写。后来在vi的基础上通过扩展插件升级就有了vim，vim是vi improve的缩写。

vim作为一款模式化编辑器，有三种模式：

1、编辑模式（也叫命令模式，是打开文件进入的默认模式）

2、输入模式

3、末行模式

具体使用教程请参考：

https://zhuanlan.zhihu.com/p/68111471

https://www.runoob.com/w3cnote/all-vim-cheatsheat.html

![vi-vim-cheat-sheet-sch](vi-vim-cheat-sheet-sch1.gif)

# vim配置

```shell
set nocompatible "不与 Vi 兼容（采用 Vim 操作命令）"
hi  comment ctermfg=4 "默认注释颜色是4，即ctermfg=4，有0，1，2，3，4，5，6，7供选:0 黑色 1  红色 2 墨绿 3 黄色 4 蓝色，即默认的颜色 5 粉色 6 淡蓝色 7  白色 "
syntax on  "开启语法高亮"
set number  "显示行号"
filetype indent on "开启文件类型检查，并且载入与该类型对应的缩进规则"
set fenc=utf-8      "文件编码"
set encoding=utf-8
set scrolloff=5     "距离顶部和底部5行"
set laststatus=2    "命令行为两行"
set backspace=2 "能使用backspace回删"
set mouse=a     "启用鼠标"
"set autoindent "按下回车键后，下一行的缩进会自动跟上一行的缩进保持一致" " 
set smartindent "每一行都和前一行有相同的缩进量，同时这种缩进形式能正确的识别出花括号，当遇到右花括号（}），则取消缩进形式;识别C语言关键字"
"set cindent     "设置C样式的缩进格式" "
set shiftwidth=4        "在文本上按下>>（增加一级缩进）、<<（取消一级缩进）或者==（取消全部缩进）时，每一级的字符数"
set tabstop=4   "设置table长度"
set textwidth=80 "设置行宽"
set showmatch   "显示匹配的括号"
set matchtime=5 "控制显示配对括号的时间"
"自动匹配括号inoremap命令用于映射按键 "
inoremap ( ()<ESC>i
inoremap [ []<ESC>i
inoremap { {}<ESC>i
inoremap < <><ESC>i
inoremap ' ''<ESC>i
inoremap " ""<ESC>i

set ignorecase      "搜索不区分大小写"
set incsearch      "输入搜索模式时，每输入一个字符，就自动跳到第一个匹配的结果"
set smartcase    "智能大小写搜索"
set hlsearch        "高亮搜索项"

set selection=inclusive "选择文本时，光标所在位置也属于被选中的范围"
set selectmode=mouse,key "使用鼠标键盘选中文本"
set wildmenu
set wildmode=longest:list,full  "命令模式下，底部操作指令按下 Tab 键自动补全。第一次按下 Tab，会显示所有匹配的操作指令的清单；第二次按下 Tab，会依次选择各个指令"

set nowrap  "设置不折行"
set wrap "自动折行，即太长的行分成几行显示"
set ruler "在状态栏显示光标的当前位置（位于哪一行哪一列）"
set undofile  "保留撤销历史"
set whichwrap+=<,>,h,l "允许backspace和光标键跨越行边界"

set showmode   "在底部显示，当前处于命令模式还是插入模式"
set showcmd    "命令模式下，在底部显示，当前键入的指令"
set showmatch  "自动高亮对应的另一个圆括号、方括号和大括号"
set guifont=Monaco:h13   "设置字体"    
set showtabline=0 "隐藏顶部标签栏"
set autoread "打开文件监视。如果在编辑过程中文件发生外部改变（比如被别的编辑器编辑了），就会发出提示"
" 隐藏滚动条"    
set guioptions-=r 
set guioptions-=L
set guioptions-=b

"插件设置"
filetype plugin on  "允许vim加载文件类型插件"
call plug#begin('~/.vim/plugged')
" Shorthand notation for plugin
Plug 'Lattay/vasp.vim'
call plug#end()
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
Plugin 'VundleVim/Vundle.vim'
call vundle#end()
filetype plugin indent on  
Plugin 'Lokaltog/vim-powerline'
Plugin 'scrooloose/nerdtree'
Plugin 'Yggdroot/indentLine'
Plugin 'jiangmiao/auto-pairs'
Plugin 'tell-k/vim-autopep8'
Plugin 'scrooloose/nerdcommenter'
Plugin 'L9'

map <F2> :NERDTreeToggle<CR> "F2开启和关闭树"
let NERDTreeChDirMode=1
"显示书签"
let NERDTreeShowBookmarks=1
"设置忽略文件类型"
let NERDTreeIgnore=['\~$', '\.pyc$', '\.swp$']
"窗口大小"
let NERDTreeWinSize=25
let g:pydiction_location = '~/.vim/tools/pydiction/complete-dict'
let g:pydiction_menu_height = 3

"缩进指示线"
let g:indentLine_char='┆'
let g:indentLine_enabled = 1
"autopep8设置"
let g:autopep8_disable_show_diff=1
"comment F4自动注释"
map <F4> <leader>ci <CR>
"默认配置文件路径"
let g:ycm_global_ycm_extra_conf = '~/.ycm_extra_conf.py'
"打开vim时不再询问是否加载ycm_extra_conf.py配置"
let g:ycm_confirm_extra_conf=0
set completeopt=longest,menu
"python解释器路径"
let g:ycm_path_to_python_interpreter='~/mambaforge3/envs/ubermag/bin/python'
"是否开启语义补全"
let g:ycm_seed_identifiers_with_syntax=1
"是否在注释中也开启补全"
let g:ycm_complete_in_comments=1
let g:ycm_collect_identifiers_from_comments_and_strings = 0
"开始补全的字符数"
let g:ycm_min_num_of_chars_for_completion=2
"补全后自动关机预览窗口"
let g:ycm_autoclose_preview_window_after_completion=1
" 禁止缓存匹配项,每次都重新生成匹配项"
let g:ycm_cache_omnifunc=0
"字符串中也开启补全"
let g:ycm_complete_in_strings = 1
"离开插入模式后自动关闭预览窗口"
autocmd InsertLeave * if pumvisible() == 0|pclose|endif

"上下左右键行为"
"inoremap <expr> <Down>    pumvisible() ? '\<C-n>' : '\<Down>'"
inoremap <expr> <Up>       pumvisible() ? '\<C-p>' : '\<Up>'
inoremap <expr> <PageDown> pumvisible() ? '\<PageDown>\<C-p>\<C-n>' : '\<PageDown>'
inoremap <expr> <PageUp>   pumvisible() ? '\<PageUp>\<C-p>\<C-n>' : '\<PageUp>'
""=============================================================================
" shell 和 python文件的注释
" ============================================================================
autocmd BufNewFile *.py,*.sh, exec ":call SetTitle()"
let $author_name = "zzmx_josh"
let $author_email = "zmxzmx1997@outlook.com"
function SetTitle()
    if &filetype == 'sh'
        call setline(1,"\#!/bin/bash")
        call append(line("."),"\# ---------------------------------------------")
        call append(line(".")+1,    "\# File Name   : ".expand("%"))
        call append(line(".")+2,    "\# Author      : ".$author_name)
        call append(line(".")+3,    "\# Mail        : ".$author_email)
        call append(line(".")+4,    "\# Date        : ".strftime('%Y-%m-%d', localtime()))
        call append(line(".")+5,    "\# Description : ")
        call append(line(".")+6,    "\# ---------------------------------------------")
        call append(line(".")+7,    "")
    else
        call setline(1,"\#!/usr/bin/python")
        call append(line("."),     "\# file name   : ".expand("%"))
        call append(line(".")+1,   "\# Author      : ".$author_name)
        call append(line(".")+2,   "\# Mail        : ".$author_email)
        call append(line(".")+3,   "\# Create Time : ".strftime('%Y-%m-%d %H:%M',localtime()))
        call append(line(".")+4,   "\# Description : ")
        call append(line(".")+5,   "")
    endif
endfunc
autocmd BufNewfile * normal G "将光标移动到新文件的最后一行"
"为Shell脚本文件添加可执行权限"
au BufWritePost * if getline(1) =~ "^#!" | if getline(1) =~ "/bin/" | silent !chmod +x <afile> | endif | endif
```



上面的配置可以直接复制，然后粘贴到~/.vimrc中使用。如果没有安装插件，请将相关的配置删除。

参考链接：

https://ruanyifeng.com/blog/2018/09/vimrc.html

https://www.cnblogs.com/litifeng/p/5651472.html

https://www.jianshu.com/p/0c83e6aed270

