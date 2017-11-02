---
title: vim 配置ctags/cscope/taglist
tags:
  - ctags
  - Tools
  - taglist
  - VIM
  - code
date: 2017-03-24 16:02:19
---

```
ctags -R --exclude=out
find . -name "*.h" -o -name "*.c" -o -name "*.cc" -o -name "*.cpp" -o -name "*.java" -o -name "*.mk" -o -name "*.xml" -o -name "*.hpp" -o -name "*.aidl" -o -name "*.AIDL" -not -path "out" > cscope.files
cscope -bkq

wget https://vim.sourceforge.io/scripts/download_script.php?src_id=19574 -O taglist_46.zip
unzip taglist_46.zip -d ~/.vim/
wget http://cscope.sourceforge.net/cscope_maps.vim
mv cscope_maps.vim ~/.vim/plugin/
```

<!--more-->

```Bash
paul@RM14:~$ cat .vimrc
nnoremap <silent> <F8> :TlistToggle<CR>

set number
set ts=4
set sw=4
set smartindent
set expandtab
set autoindent
set cursorline

let Tlist_Auto_Open=1
let Tlist_Exit_OnlyWindow=1
let Tlist_Use_Right_Window=1

set tags =tags;
set autochdir

:color desert

let cscope_file=findfile("cscope.out", ".;")
let cscope_pre=matchstr(cscope_file, ".*/")
"echo cscope_file
if !empty(cscope_file) &amp;&amp; filereadable(cscope_file)
exe "cs add" cscope_file cscope_pre
endif
```

```
:cs find {querytype} {name}
{querytype}:
0 or s: Find this C symbol
1 or g: Find this difinition
2 or d: Find functions called by this function
3 or c: Find functions calling this function
4 or t: Find this text string
6 or e: Find this egrep pattern
7 or f: Find this file
8 or i: Find files #including this file
```
