> 接上篇windows安装vim，链接：https://blog.csdn.net/qq_52397471/article/details/122351600

闲着无聊，对vim的配置又整蛊了一番！

> 整不整只是个人喜好，不喜欢可以跳过。有兴趣了可以整整！

## 效果

下面是我的效果图：

![image-20221230143357395](.\images\image-20221230143357395.png)

本次主要安装了一些插件并且把配置整理了一下。在我看来，我用起来是更顺手了一些。

## 主要功能预览：

1. 生成固定的头文件配置

   ![image-20221230143744977](.\images\image-20221230143744977.png)

   ![image-20221230143801370](.\images\image-20221230143801370.png)

   加入了一些常用插件：eg：nerdTree，coc，rainbow

   ![image-20221230143907319](.\images\image-20221230143907319.png)

   ![image-20221230143925905](.\images\image-20221230143925905.png)

删除了一些对我来说不必要的配置。

## 配置

下面贴出我的vim配置

```vim
" __  ____   __  _   ___     _____ __  __ ____   ____
"|  \/  \ \ / / | \ | \ \   / /_ _|  \/  |  _ \ / ___|
"| |\/| |\ V /  |  \| |\ \ / / | || |\/| | |_) | |
"| |  | | | |   | |\  | \ V /  | || |  | |  _ <| |___
"|_|  |_| |_|   |_| \_|  \_/  |___|_|  |_|_| \_\\____|

" Author: @yuluo
" ===================== Vim Init =======================

" Vim with all enhancements
source $VIMRUNTIME/vimrc_example.vim

" Remap a few keys for Windows behavior
source $VIMRUNTIME/mswin.vim

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

" ===================== Normal Settings =======================
set nobackup  		"取消生成备份文件
set number 		" 设置行号
syntax on		" 语法高亮。自动识别代码，使用多种颜色表示
set t_Co=256		"256色
colorscheme gruvbox	" 选择颜色主题(已经下载好并放到$VIM/vim82/colors文件夹下) 
set guifont=Consolas:h12	" 设置gvim的字体
vmap <C-c> "+y		" 设置gvim下和外部的复制粘贴
vmap <C-x> "+c
vmap <C-v> c<ESC>"+p
imap <C-v> <C-r><C-o>+
set mouse=a		" 支持使用鼠标
set autoindent		" 按下回车键后，下一行的缩进会自动跟上一行的缩进保持一致
set tabstop=4		" Tab 空格数
set shiftwidth=4		" normal模式下，>>增加一级缩进、<<取消一级缩进、==取消全部缩进时，每一级缩进的空格数
set expandtab		" 自动将Tab转为空格(防止Tab键在不同编辑器缩进不一致导致问题)
set softtabstop=4		" Tab转为多少个空格
set cursorline		" 光标所在行高亮
hi CursorLine term=bold cterm=bold ctermbg=237
set nowrap		" 关闭自动折行
set scrolloff=5		" 垂直滚动时，光标距离顶部/底部的距离（单位：行）
set sidescrolloff=30	" 水平滚动时，光标距离行首或行尾的距离（单位：字符）
set textwidth=1000	" 设置行宽，即一行显示多少字符
set laststatus=2		" 是否显示状态栏：0表示不显示，1表示只在多窗口显示，2表示显示

" ============此处我使用插件==========
" 设置状态条显示的信息：文件名、光标所在字符的ASCII码、光标所在字符的ASCII码的十六进制、光标所在的位置、光标所在行之上的内容占整个文件的百分比、文件总行数
" set statusline=\ %F%m%r%h%w\ \ \ \ ASCII=\%03.3b\ \ \ \ HEX=\%02.2B\ \ \ \ POS=%04l,%04v\ \ \ \ %p%%\ \ \ \ NumOfLine=%L

highlight WhitespaceEOL ctermbg=red guibg=red	" 显示行尾的空格
match WhitespaceEOL /\s\+$/
set showmatch		" 光标遇到括号时，自动高亮对应的另一半括号
set showcmd		" 命令行模式下，在底部显示当前键入的指令。例如键入dd删除一行时，键入第一个d，底部右侧显示d，完全键入dd时，操作完成，底部显示消失
set hlsearch		" 搜索时，高亮显示搜索结果
set incsearch		" 搜索时，每输入一个字符，自动跳到第一个匹配的结果
set ignorecase		" 搜索时忽略大小写
set noswapfile		" 不创建交换文件
set undofile		" 保留 撤销 操作历史
set undodir=$VIM\.undo	" 设置操作历史文件的保存位置
set history=1000		" vim需要记住多少次历史操作
set wildmenu		" 命令模式下，底部操作指令按下 Tab 键自动补全。第一次按下 Tab，会显示所有匹配的操作指令的清单；第二次按下 Tab，会依次选择各个指令
set wildmode=longest:list,full
set encoding=utf-8	"设置文件的代码形式 utf8
set termencoding=utf-8
set fileencoding=utf-8
set fileencodings=ucs-bom,utf-8,chinese,cp936

map <F1> <C-w>w	" 定义F1快捷键为切换vim窗口

" ===================== Install Plugins with Vim-Plug =======================
call plug#begin('~/.vim/plugged')
Plug 'octol/vim-cpp-enhanced-highlight'
Plug 'neoclide/coc.nvim', {'branch': 'release'}
Plug 'jiangmiao/auto-pairs'
Plug 'vim-airline/vim-airline'
Plug 'vim-airline/vim-airline-themes'
Plug 'Yggdroot/indentLine'
Plug 'luochen1990/rainbow'
Plug 'preservim/nerdtree'
Plug 'fatih/vim-go' , { 'for': ['go', 'vim-plug'], 'tag': '*' }
call plug#end()

" ===================== needTree =======================
" 使用Ctrl N 打开窗口
map <C-n> :NERDTreeToggle<CR>

" ===================== indentline =======================
let g:indentLine_setColors = 0

" ===================== rainbow =======================
let g:rainbow_active = 1 "0 if you want to enable it later via :RainbowToggle

" ===================== AutoPairs =======================
let g:AutoPairs={'(':')', '[':']', '{':'}', '"':'"', "`":"`", '```':'```', '"""':'"""', "'''":"'''"}

" ==================== vim-go ====================
let g:go_echo_go_info = 0
let g:go_doc_popup_window = 1
let g:go_def_mapping_enabled = 0
let g:go_template_autocreate = 0
let g:go_textobj_enabled = 0
let g:go_auto_type_info = 1
let g:go_def_mapping_enabled = 0
let g:go_highlight_array_whitespace_error = 1
let g:go_highlight_build_constraints = 1
let g:go_highlight_chan_whitespace_error = 1
let g:go_highlight_extra_types = 1
let g:go_highlight_fields = 1
let g:go_highlight_format_strings = 1
let g:go_highlight_function_calls = 1
let g:go_highlight_function_parameters = 1
let g:go_highlight_functions = 1
let g:go_highlight_generate_tags = 1
let g:go_highlight_methods = 1
let g:go_highlight_operators = 1
let g:go_highlight_space_tab_error = 1
let g:go_highlight_string_spellcheck = 1
let g:go_highlight_structs = 1
let g:go_highlight_trailing_whitespace_error = 1
let g:go_highlight_types = 1
let g:go_highlight_variable_assignments = 0
let g:go_highlight_variable_declarations = 0
let g:go_doc_keywordprg_enabled = 0

" ===================== airline =======================
let g:airline#extensions#tabline#enabled = 1
let g:airline#extensions#tabline#left_sep = ''
let g:airline#extensions#tabline#left_alt_sep = ''
let g:airline#extensions#tabline#formatter = 'unique_tail'
let g:airline#extensions#tabline#buffer_nr_show = 1        "显示buffer编号
let g:airline#extensions#tabline#buffer_nr_format = '%s:'
let g:airline#extensions#battery#enabled = 1
let g:airline_left_sep = ''
let g:airline_left_alt_sep = ''
let g:airline_theme='onedark' 
let g:airline_powerline_fonts = 1  " 支持 powerline 字体

" ===================== File Templates =======================
"新建.c,.h,.sh,.java文件，自动插入文件头 
autocmd BufNewFile *.go,*.[ch],*.sh exec ":call SetTitle()" 

func SetTitle()  
    if &filetype == 'sh' 
        call setline(1, "########################################################################") 
        call append(line("."), "# File Name: ".expand("%")) 
        call append(line(".")+1, "# Author: yuluo") 
        call append(line(".")+2, "# Email: yuluo@163.com") 
        call append(line(".")+3, "# Created Time: ".strftime("%c")) 
        call append(line(".")+4, "########################################################################") 
        call append(line(".")+5, "#!/bin/sh")
        call append(line(".")+6, "")
    elseif &filetype == 'c'
        call setline(1, "########################################################################") 
        call append(line("."), "# File Name: ".expand("%")) 
        call append(line(".")+1, "# Author: yuluo") 
        call append(line(".")+2, "# Email: yuluo@163.com") 
        call append(line(".")+3, "# Created Time: ".strftime("%c")) 
        call append(line(".")+4, "########################################################################") 
        call append(line(".")+5, "")
        call append(line(".")+6, "#include<stdio.h>")
        call append(line(".")+7, "")
        call append(line(".")+8, "int main(int argc, char *argv[])")
        call append(line(".")+9, "{")
        call append(line(".")+10, "    ")
        call append(line(".")+11, "    return 0;")
        call append(line(".")+12, "}")
    else
        call setline(1, "########################################################################") 
        call append(line("."), "# File Name: ".expand("%")) 
        call append(line(".")+1, "# Author: yuluo") 
        call append(line(".")+2, "# Email: yuluo@163.com") 
        call append(line(".")+3, "# Created Time: ".strftime("%c")) 
        call append(line(".")+4, "########################################################################") 
        call append(line(".")+5, "")
        call append(line(".")+6, "package main")
        call append(line(".")+7, "")
        call append(line(".")+8, "import \"fmt\" ")
        call append(line(".")+9, "")
        call append(line(".")+10, "func main() {")
        call append(line(".")+11, "    ")
        call append(line(".")+12, "}")
   endif
endfunc

 "自动定位到文件末尾
autocmd BufNewFile * normal G
```



