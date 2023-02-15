# WIndows安装vim

## 起因：

> 寒假在家闲得无聊，在对vsode折腾了一番后，感觉老不顺手，有一股力不从心之感。
>
> 于是便突发奇想，在windows安装vim。
>
> 话不多说，开始整活

## 下载vim在windows的版本 **`gvim`**

gvim：[下载链接]([Releases · vim/vim-win32-installer (github.com)](https://github.com/vim/vim-win32-installer/releases))

![5](C:/Users/14815/Desktop/5.png)

注意：无其他要求，下载之后正常安装即可，如果有其他需求，自行百度安装方法，

建议在```键盘映射和鼠标做一些修改，符合自己的使用习惯即可```

![6](C:/Users/14815/Desktop/6.png)

如果不出意外，安装完成！打开之后会有如下界面：

![7](C:/Users/14815/Desktop/7.png)

## 配置环境变量

> 配置环境变量为了可以像linux一样在terminal使用vim

打开vim的安装目录，将gim所在的文件夹路径复制到path中即可，vim和gvim在同一个文件夹中

## 配置vim

安装完成了之后，最重要的事情就是配置vim，让你的vim看起来更加花哨，同时以**增加实用性为主**

### 基本设置

配置vim的地方和linux差不多，在vim安装夹下的_vimrc 文件中。可以参考linux的vim设置 

为了进行颜色主题配置，首先下载solarized主题，从[这里]([Solarized (ethanschoonover.com)](https://ethanschoonover.com/solarized/))下载后将里面的solarized.vim文件放到安装目录下的vim82文件夹中的colors文件夹下。

对于配色方案，个人感觉mokolai的主题挺不错，在知乎上呼声很高，建议尝试！

在配置某个配置之前，可以先查看一下目前默认的是否开启，如果开启就不用配置了。具体查看方法是，在normal模式下，输入该配置，并加上“？”回车。例如：:set number?表示查看是否设置行号。
在安装目录下新建.undo文件夹，也可放在自己喜欢的位置，用来保存历史文件（需要在.vimrc中设置，即下面的配置信息中的undodir），使得某个文件关闭后，再次打开也可以还原之前的更改。

以下是我的个人设置

```shell
" 基本配置
" 
"
" 设置行号
set number
" 语法高亮。自动识别代码，使用多种颜色表示
syntax enable
" 选择颜色主题(已经下载好并放到$VIM/vim82/colors文件夹下) ，推荐自行下载使用solarized主题
colorscheme solarized
" 设置gvim的字体
set guifont=Consolas:h12
" 设置gvim下和外部的复制粘贴
vmap <C-c> "+y
vmap <C-x> "+c
vmap <C-v> c<ESC>"+p
imap <C-v> <C-r><C-o>+
" 支持使用鼠标
set mouse=a
" 按下回车键后，下一行的缩进会自动跟上一行的缩进保持一致
set autoindent
" 按下Tab键后，vim显示的空格数
set tabstop=4
" normal模式下，>>增加一级缩进、<<取消一级缩进、==取消全部缩进时，每一级缩进的空格数
set shiftwidth=4
" 自动将Tab转为空格(防止Tab键在不同编辑器缩进不一致导致问题)
set expandtab
" Tab转为多少个空格
set softtabstop=4
" 光标所在行高亮
set cursorline
highlight CursorLine   cterm=NONE ctermbg=black guibg=NONE guifg=NONE
" 关闭自动折行
set nowrap
" 垂直滚动时，光标距离顶部/底部的距离（单位：行）
set scrolloff=5
" 水平滚动时，光标距离行首或行尾的距离（单位：字符）
set sidescrolloff=30
" 设置行宽，即一行显示多少字符
set textwidth=1000
" 是否显示状态栏：0表示不显示，1表示只在多窗口显示，2表示显示
set laststatus=2
" 设置状态条显示的信息：文件名、光标所在字符的ASCII码、光标所在字符的ASCII码的十六进制、光标所在的位置、光标所在行之上的内容占整个文件的百分比、文件总行数
set statusline=\ %F%m%r%h%w\ \ \ \ ASCII=\%03.3b\ \ \ \ HEX=\%02.2B\ \ \ \ POS=%04l,%04v\ \ \ \ %p%%\ \ \ \ NumOfLine=%L
" 显示行尾的空格
highlight WhitespaceEOL ctermbg=red guibg=red
match WhitespaceEOL /\s\+$/
" 光标遇到括号时，自动高亮对应的另一半括号
set showmatch
" 命令行模式下，在底部显示当前键入的指令。例如键入dd删除一行时，键入第一个d，底部右侧显示d，完全键入dd时，操作完成，底部显示消失
set showcmd
" 搜索时，高亮显示搜索结果
set hlsearch
" 搜索时，每输入一个字符，自动跳到第一个匹配的结果
set incsearch
" 搜索时忽略大小写
set ignorecase
" 不创建交换文件
set noswapfile
" 保留 撤销 操作历史
set undofile
" 设置操作历史文件的保存位置
set undodir=$VIM\.undo
" vim需要记住多少次历史操作
set history=1000
" 命令模式下，底部操作指令按下 Tab 键自动补全。第一次按下 Tab，会显示所有匹配的操作指令的清单；第二次按下 Tab，会依次选择各个指令
set wildmenu
set wildmode=longest:list,full
" 定义F1快捷键为切换vim窗口
map &ltF1&gt &ltC-w&gtw
"设置文件的代码形式 utf8
set encoding=utf-8
set termencoding=utf-8
set fileencoding=utf-8
set fileencodings=ucs-bom,utf-8,chinese,cp936

" 文件配置
"新建.c,.h,.sh,.java文件，自动插入文件头 
autocmd BufNewFile *.cpp,*.[ch],*.sh,*.java exec ":call SetTitle()" 
""定义函数SetTitle，自动插入文件头 
func SetTitle() 
	"如果文件类型为.sh文件 
	if &filetype == 'sh' 
		call setline(1, "##########################################################################") 
		call append(line("."), "# File Name: ".expand("%")) 
		call append(line(".")+1, "# Author: amoscykl") 
		call append(line(".")+2, "# mail: amoscykl980629@163.com") 
		call append(line(".")+3, "# Created Time: ".strftime("%c")) 
		call append(line(".")+4, "#########################################################################") 
		call append(line(".")+5, "#!/bin/zsh")
		call append(line(".")+6, "PATH=/home/edison/bin:/home/edison/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/work/tools/gcc-3.4.5-glibc-2.3.6/bin")
		call append(line(".")+7, "export PATH")
		call append(line(".")+8, "")
	else 
		call setline(1, "/*************************************************************************") 
		call append(line("."), "	> File Name: ".expand("%")) 
		call append(line(".")+1, "	> Author: amoscykl") 
		call append(line(".")+2, "	> Mail: amoscykl@163.com ") 
		call append(line(".")+3, "	> Created Time: ".strftime("%c")) 
		call append(line(".")+4, " ************************************************************************/") 
		call append(line(".")+5, "")
	endif
	if &filetype == 'cpp'
		call append(line(".")+6, "#include<iostream>")
    	call append(line(".")+7, "using namespace std;")
		call append(line(".")+8, "")
	endif
	if &filetype == 'c'
		call append(line(".")+6, "#include<stdio.h>")
		call append(line(".")+7, "")
	endif
	"	if &filetype == 'java'
	"		call append(line(".")+6,"public class ".expand("%"))
	"		call append(line(".")+7,"")
	"	endif
	"新建文件后，自动定位到文件末尾
	autocmd BufNewFile * normal G
endfunc
```

### 插件配置

vim被称为上古编辑器之神，最大的优点在于全键盘操作之后，就是插件，插件为vim的生态做出了巨大贡献，使得vim长存不灭

首先安装vim插件管理器vundle

```git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim```

注意此处路劲是linux下的vim配置路径，windows应为：(没有新建文件夹即可)

```git clone https://github.com/VundleVim/Vundle.vim.git F:\Vim\vimfiles\bundle```

> 如果没有安装git，在安装git之后再执行上述命令

然后在vimrc中添加如下配置

```shell
" Vundle配置
set nocompatible              " 设置vim和vi不兼容。在兼容模式下运行时，Vim 大部分增强及改善的功能就不可用了,vundle要求必须有
filetype off                  " required
" set the runtime path to include Vundle and initialize
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
" alternatively, pass a path where Vundle should install plugins
"call vundle#begin('~/some/path/here')

" let Vundle manage Vundle, required
Plugin 'VundleVim/Vundle.vim'
" Keep Plugin commands between vundle#begin/end.

" 在vim中使用git命令
Plugin 'tpope/vim-fugitive'
" 在vim行号旁显示git diff的差异标记
Plugin 'airblade/vim-gitgutter'

" All of your Plugins must be added before the following line
call vundle#end()            " required
filetype plugin indent on    " required
" To ignore plugin indent changes, instead use:
"filetype plugin on
"
" Brief help
" :PluginList       - lists configured plugins
" :PluginInstall    - installs plugins; append `!` to update or just :PluginUpdate
" :PluginSearch foo - searches for foo; append `!` to refresh local cache
" :PluginClean      - confirms removal of unused plugins; append `!` to auto-approve removal
"
" see :h vundle for more details or wiki for FAQ
" Put your non-Plugin stuff after this line

```

### 插件安装方法

针对Vundel而言，插件安装方法主要是在__vimrc中进行相应的配置，然后在vim的命令行中执行```PluginInstall```即可安装插件

>  vim插件推荐:
>
>  **Vim-Rainbow** : 几乎所有主流的编程语言都需要用到方括号，圆括号，花括号等，通常，我们会使用多对的括号，要弄清楚哪个右括号属于哪个左括号可能会变得困难和烦人。而vim-rainbow插件会给每一对括号加一个独特的颜色，这样你就很容易辨认了，而且它不仅很有用，也会让你的代码变得丰富多彩
>
> **lightline**: Vim的插件有很多，比如Powerline，它在屏幕的底部放一个栏，告诉你你在处理什么文件，你在文件的什么位置，它是什么类型的文件等等，每个插件都有优点和缺点，在权衡之下，我最后选择了lightline。它相对较小，易于设置，如果你对这类东西感兴趣，它具有相当的可扩展性，并且不需要任何其他工具或插件。
>
> 当然，如果跟着我的教程正在动手做的人会发现我已经设置了底部的状态栏。不过这个更加花哨罢了
>
> **NERDTree**：该插件显示一个目录树，可以执行文件的操作命令。在大型项目中，你很难找到你要编辑的那一行的文件确切名称和位置。通过快捷键，一个资源管理器窗口就会以目录树的方式打开，你可以轻松找到你想要的文件并打开它，假如你有大量代码或者容易忘记文件名的人，NERDTree是必须要有的。
>
>  **Tag List**：如果你在一个文件中进行编程，那你很容易就忘记你所处的位置，你可能需要自己手动上下滚动查找某个函数，有了Tag List插件，你只需输入:Tlist，就可以得到一个垂直分割，其中包含可以轻松跳转到的变量、类型、类和函数。它适用于许多语言，比如Java、Python，以及ctags工具可以处理的任何其他文件类型…
>
> **YouCompleteMe**：效率最高的vim补全插件 
>
> **leaderF**：提到vim的模糊查找插件，很多人第一反应是ctrlp.vim，ctrlp知名度很高，但跟其它的同类插件相比，它的唯一优点是用vimL编写（这让它的性能在同类插件中并不算优秀）。这里向大家推荐一款模糊查找插件——LeaderF，无论是从性能还是匹配精度上，都远远超越ctrlp[3]
>
>  **auto-pairs**：这个就是插件的功能简单而实用：**在输入/删除左括号时，能自动补上/删除右括号**。

下面演示Vundle的插件安装方法：

```shell
" 所有的插件安装必须在此行语句之后
call vundle#begin()

" 让vundle管理插件版本,必须
Plugin 'VundleVim/Vundle.vim'

" Github上的插件
" 格式为 Plugin '用户名/插件仓库名'
Plugin 'tpope/vim-fugitive'


" 你的所有插件需要在下面这行之前
call vundle#end()            

 " 必须 加载vim自带和插件相应的语法和文件类型相关脚本
filetype plugin indent on   
```

示例：(安装代码补全插件)

```shell
""""""""""""""""""""""""""""""""""""""""""
"Vundle插件安装部分
""""""""""""""""""""""""""""""""""""""""""
" 所有的插件安装必须在此行语句之后
call vundle#begin()

" 让vundle管理插件版本,必须
Plugin 'VundleVim/Vundle.vim'

Plugin 'ycm-core/YouCompleteMe'

" 你的所有插件需要在下面这行之前
call vundle#end()            

 " 必须 加载vim自带和插件相应的语法和文件类型相关脚本
filetype plugin indent on  
```

![image-20220106193744119](C:/Users/14815/Desktop/image-20220106193744119.png)

等待安装进程结束即可

## 下面贴出我的windows系统的vim配置

```shell
" 基本配置
" 
"
" 设置行号
set number
" 语法高亮。自动识别代码，使用多种颜色表示
syntax enable
" 选择颜色主题(已经下载好并放到$VIM/vim82/colors文件夹下) ，推荐自行下载使用solarized主题
colorscheme molokai 
" 设置gvim的字体
set guifont=Consolas:h12
" 设置gvim下和外部的复制粘贴
vmap <C-c> "+y
vmap <C-x> "+c
vmap <C-v> c<ESC>"+p
imap <C-v> <C-r><C-o>+
" 支持使用鼠标
set mouse=a
" 按下回车键后，下一行的缩进会自动跟上一行的缩进保持一致
set autoindent
" 按下Tab键后，vim显示的空格数
set tabstop=4
" normal模式下，>>增加一级缩进、<<取消一级缩进、==取消全部缩进时，每一级缩进的空格数
set shiftwidth=4
" 自动将Tab转为空格(防止Tab键在不同编辑器缩进不一致导致问题)
set expandtab
" Tab转为多少个空格
set softtabstop=4
" 光标所在行高亮
set cursorline
highlight CursorLine   cterm=NONE ctermbg=black guibg=NONE guifg=NONE
" 关闭自动折行
set nowrap
" 垂直滚动时，光标距离顶部/底部的距离（单位：行）
set scrolloff=5
" 水平滚动时，光标距离行首或行尾的距离（单位：字符）
set sidescrolloff=30
" 设置行宽，即一行显示多少字符
set textwidth=1000
" 是否显示状态栏：0表示不显示，1表示只在多窗口显示，2表示显示
set laststatus=2
" 设置状态条显示的信息：文件名、光标所在字符的ASCII码、光标所在字符的ASCII码的十六进制、光标所在的位置、光标所在行之上的内容占整个文件的百分比、文件总行数
set statusline=\ %F%m%r%h%w\ \ \ \ ASCII=\%03.3b\ \ \ \ HEX=\%02.2B\ \ \ \ POS=%04l,%04v\ \ \ \ %p%%\ \ \ \ NumOfLine=%L
" 显示行尾的空格
highlight WhitespaceEOL ctermbg=red guibg=red
match WhitespaceEOL /\s\+$/
" 光标遇到括号时，自动高亮对应的另一半括号
set showmatch
" 命令行模式下，在底部显示当前键入的指令。例如键入dd删除一行时，键入第一个d，底部右侧显示d，完全键入dd时，操作完成，底部显示消失
set showcmd
" 搜索时，高亮显示搜索结果
set hlsearch
" 搜索时，每输入一个字符，自动跳到第一个匹配的结果
set incsearch
" 搜索时忽略大小写
set ignorecase
" 不创建交换文件
set noswapfile
" 保留 撤销 操作历史
set undofile
" 设置操作历史文件的保存位置
set undodir=$VIM\.undo
" vim需要记住多少次历史操作
set history=1000
" 命令模式下，底部操作指令按下 Tab 键自动补全。第一次按下 Tab，会显示所有匹配的操作指令的清单；第二次按下 Tab，会依次选择各个指令
set wildmenu
set wildmode=longest:list,full
" 定义F1快捷键为切换vim窗口
map &ltF1&gt &ltC-w&gtw
"设置文件的代码形式 utf8
set encoding=utf-8
set termencoding=utf-8
set fileencoding=utf-8
set fileencodings=ucs-bom,utf-8,chinese,cp936
let g:rehash256 = 1

"新建.c,.h,.sh,.java文件，自动插入文件头 
autocmd BufNewFile *.cpp,*.[ch],*.sh,*.java exec ":call SetTitle()" 
""定义函数SetTitle，自动插入文件头 
func SetTitle() 
	"如果文件类型为.sh文件 
	if &filetype == 'sh' 
		call setline(1, "##########################################################################") 
		call append(line("."), "# File Name: ".expand("%")) 
		call append(line(".")+1, "# Author: amoscykl") 
		call append(line(".")+2, "# mail: amoscykl980629@163.com") 
		call append(line(".")+3, "# Created Time: ".strftime("%c")) 
		call append(line(".")+4, "#########################################################################") 
		call append(line(".")+5, "#!/bin/zsh")
		call append(line(".")+6, "PATH=/home/edison/bin:/home/edison/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/work/tools/gcc-3.4.5-glibc-2.3.6/bin")
		call append(line(".")+7, "export PATH")
		call append(line(".")+8, "")
	else 
		call setline(1, "/*************************************************************************") 
		call append(line("."), "	> File Name: ".expand("%")) 
		call append(line(".")+1, "	> Author: amoscykl") 
		call append(line(".")+2, "	> Mail: amoscykl@163.com ") 
		call append(line(".")+3, "	> Created Time: ".strftime("%c")) 
		call append(line(".")+4, " ************************************************************************/") 
		call append(line(".")+5, "")
	endif
	if &filetype == 'cpp'
		call append(line(".")+6, "#include<iostream>")
    	call append(line(".")+7, "using namespace std;")
		call append(line(".")+8, "")
	endif
	if &filetype == 'c'
		call append(line(".")+6, "#include<stdio.h>")
		call append(line(".")+7, "")
	endif
	"	if &filetype == 'java'
	"		call append(line(".")+6,"public class ".expand("%"))
	"		call append(line(".")+7,"")
	"	endif
	"新建文件后，自动定位到文件末尾
	autocmd BufNewFile * normal G
endfunc

""""""""""""""""""""""""""""""""""""""""""
"Vundle插件安装部分
""""""""""""""""""""""""""""""""""""""""""
" 所有的插件安装必须在此行语句之后
call vundle#begin()

" 让vundle管理插件版本,必须
Plugin 'VundleVim/Vundle.vim'

Plugin 'ycm-core/YouCompleteMe'
Plugin 'luochen1990/rainbow'

" 你的所有插件需要在下面这行之前
call vundle#end()            

 " 必须 加载vim自带和插件相应的语法和文件类型相关脚本
filetype plugin indent on  

"""""""""""""""""""""""""""""""""""""""""""
"插件配置部分
"""""""""""""""""""""""""""""""""""""""""""
 let g:rainbow_active = 1 "0 if you want to enable it later via :RainbowToggle
	let g:rainbow_conf = {
	\	'guifgs': ['royalblue3', 'darkorange3', 'seagreen3', 'firebrick'],
	\	'ctermfgs': ['lightblue', 'lightyellow', 'lightcyan', 'lightmagenta'],
	\	'operators': '_,_',
	\	'parentheses': ['start=/(/ end=/)/ fold', 'start=/\[/ end=/\]/ fold', 'start=/{/ end=/}/ fold'],
	\	'separately': {
	\		'*': {},
	\		'tex': {
	\			'parentheses': ['start=/(/ end=/)/', 'start=/\[/ end=/\]/'],
	\		},
	\		'lisp': {
	\			'guifgs': ['royalblue3', 'darkorange3', 'seagreen3', 'firebrick', 'darkorchid3'],
	\		},
	\		'vim': {
	\			'parentheses': ['start=/(/ end=/)/', 'start=/\[/ end=/\]/', 'start=/{/ end=/}/ fold', 'start=/(/ end=/)/ containedin=vimFuncBody', 'start=/\[/ end=/\]/ containedin=vimFuncBody', 'start=/{/ end=/}/ fold containedin=vimFuncBody'],
	\		},
	\		'html': {
	\			'parentheses': ['start=/\v\<((area|base|br|col|embed|hr|img|input|keygen|link|menuitem|meta|param|source|track|wbr)[ >])@!\z([-_:a-zA-Z0-9]+)(\s+[-_:a-zA-Z0-9]+(\=("[^"]*"|'."'".'[^'."'".']*'."'".'|[^ '."'".'"><=`]*))?)*\>/ end=#</\z1># fold'],
	\		},
	\		'css': 0,
	\	}
	\}

" Vim with all enhancements
source $VIMRUNTIME/vimrc_example.vim

" Remap a few keys for Windows behavior
source $VIMRUNTIME/mswin.vim

" Mouse behavior (the Windows way)
behave mswin

" Use the internal diff if available.
" Otherwise use the special 'diffexpr' for Windows.
if &diffopt !~# 'internal'
  set diffexpr=MyDiff()
endif
function MyDiff()
  let opt = '-a --binary '
  if &diffopt =~ 'icase' | let opt = opt . '-i ' | endif
  if &diffopt =~ 'iwhite' | let opt = opt . '-b ' | endif
  let arg1 = v:fname_in
  if arg1 =~ ' ' | let arg1 = '"' . arg1 . '"' | endif
  let arg1 = substitute(arg1, '!', '\!', 'g')
  let arg2 = v:fname_new
  if arg2 =~ ' ' | let arg2 = '"' . arg2 . '"' | endif
  let arg2 = substitute(arg2, '!', '\!', 'g')
  let arg3 = v:fname_out
  if arg3 =~ ' ' | let arg3 = '"' . arg3 . '"' | endif
  let arg3 = substitute(arg3, '!', '\!', 'g')
  if $VIMRUNTIME =~ ' '
    if &sh =~ '\<cmd'
      if empty(&shellxquote)
        let l:shxq_sav = ''
        set shellxquote&
      endif
      let cmd = '"' . $VIMRUNTIME . '\diff"'
    else
      let cmd = substitute($VIMRUNTIME, ' ', '" ', '') . '\diff"'
    endif
  else
    let cmd = $VIMRUNTIME . '\diff'
  endif
  let cmd = substitute(cmd, '!', '\!', 'g')
  silent execute '!' . cmd . ' ' . opt . arg1 . ' ' . arg2 . ' > ' . arg3
  if exists('l:shxq_sav')
    let &shellxquote=l:shxq_sav
  endif
endfunction


```

