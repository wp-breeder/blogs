### vim常用配置

```shell
#打开vim配置文件
vi ~/.vimrc
################################
#以下是vim配置文件内容
" 设置tab为4个空格
set ts=4
set expandtab
"set nu
set shiftwidth=4
set autoindent
set cindent
"" 增强模式中的命令行自动完成操作
"set wildmenu

" 在状态行上显示光标所在位置的行号和列号
set ruler
set rulerformat=%20(%2*%<%f%=\ %m%r\ %3l\ %c\ %p%%%)
" 在被分割的窗口间显示空白，便于阅读
set fillchars=vert:\ ,stl:\ ,stlnc:\
" 光标移动到buffer的顶部和底部时保持3行距离
set scrolloff=3
" 我的状态行显示的内容（包括文件类型和解码）
set statusline=%F%m%r%h%w\[POS=%l,%v][%p%%]\%{strftime(\"%d/%m/%y\ -\ %H:%M\")}
" 总是显示状态行
set laststatus=2
```

