Title: 编辑器之神vim养成
Date: 2014-1-16
Modified: 2014-1-16
Tags: ubuntu, vim
Slug: setupvim

坐电脑面前，写文章敲代码，大部分时间是和编辑器交互的，所以编辑器效率高不高，好不好用体验都是最直接的。以前也试过Sublime
Text，不过没有上手。编辑器之神vim的大名倒是早有耳闻，不过陡峭的学习曲线也不是盖的，所以也一直不敢亵玩。倒是最近转到linux下面，整天泡在终端里不出来，vim来vim去的倒也慢慢的喜欢上了，昨天一咬牙，找了参考自己配置了一番，硬把原本不比windows下面记事本高端的玩意儿武装成了战斗机，最大的感受是，好玩！下面把我的配置写在这里，留作参考。

开始之前先说说我是怎么上手vim的。窃以为学习使用vim，一开始的生存阶段能否坚持下来是最重要的，而我一开始是从使用chrome的一个叫[Vimium](https://chrome.google.com/webstore/detail/vimium/dbepggeogbaibhgnhhndojpepiihcmeb)的插件开始的。装上以后可以用vim的浏览模式看网页，所有操作都可以通过键盘执行，尤其是我经常包着笔记本又懒得带鼠标，简直吊且酷。这无疑是个不错的切入点，有不懂的按一下问号查帮助，很方便。等基本操作熟悉了以后再来学着用vim就自然的多。至于说明书、教程之类的网上很多，这里就不废话了。

#配置vim

vim的配置我认为在于两个方面，一是vim本身提供的各种设置选项，通过写配置文件`~/.vimrc`来调整，二是安装插件，这个就看想象力了。两者相结合，vim登神。

##写配置文件

我把我的配置文件贴在下面，写注释花了不少时间，不过是值得的。

    :::vim
    "不要兼容vi
    set nocompatible

    "允许鼠标的使用
    set mouse=a

    "打开自动高亮
    syntax on

    "选择色彩方案
    colorscheme desert
    set background=dark
    set t_Co=256

    "设置窗口大小
    set lines=40
    set columns=130
     
    "文件被改变时自动读取
    set autoread

    "显示行号
    set number


    "Set 7 lines to the curso - when moving vertically using j/k
    "set so=7

    "打开wildmenu，用于命令行补完。比如在命令行输入 :color <Tab> 会出现一排电脑上可用的色彩方案，按<Tab>就能切换
    set wildmenu

    "总显示当前位置，见右下角
    set ruler

    "高亮搜索结果
    set hlsearch

    "在输入搜索文字时，实时匹配
    set incsearch

    "显示配对的括号
    set showmatch

    "统一编码
    set encoding=utf8


    "将tab换成空格
    set expandtab

    "智能tab
    set smarttab

    "1tab==4空格
    set shiftwidth=4
    set tabstop=4

    "自动断行
    set lbr
    set tw=500

    "自动缩进
    set ai

    "智能缩进
    set si

    "自动换行
    set wrap

    "在分屏中间方便移动的映射
    map <C-j> <C-W>j
    map <C-k> <C-W>k
    map <C-h> <C-W>h
    map <C-l> <C-W>l

##安装插件

安装插件的方法比较多，这里推荐用vundle，github项目主页在[这里](https://github.com/gmarik/vundle)。需要注意的是vundle需要git的支持。

###vundle的安装

把vundle从github上克隆下来：
    
    :::bash
    $ git clone https://github.com/gmarik/vundle.git ~/.vim/bundle/vundle

剩下的配置在`.vimrc`里写

    :::vim
    "Vundle vim的插件管理程序{
    """"""""
    filetype off
    set rtp+=~/.vim/bundle/vundle/
    call vundle#rc()
    " 让Bundle管理Vundle
    Bundle 'gmarik/vundle'
    """"""""以上为必须的部分

    filetype plugin indent on     " 必须的

这样vundle就配置好了，如果需要安装插件则可以在上面的语句后面添加如下的语句，我以安装`autoclose`这个插件为例

    :::vim
    Bundle 'AutoClose'

保存之后再打开一个vim的窗口，在命令行里输入`:BundleList`，可以看到你的插件列表，输入`:BundleInstall`就可以对列表里面的插件安装/升级了。

###插件来源

首先你可以去[vim-scripts.org](http://vim-scripts.org/vim/scripts.html)上搜搜看，现在插件作者们都在把自己的作品往这上面迁移，还是比较全的。找到心仪的插件后复制一下名字，在`.vimrc`后面添加语句
    
    :::vim
    Bundle '插件名'

按上面的步骤安装就好。

另外托管在github上的项目也是支持的，下面的两种写法都是合法的，注意名字中间不要有空格

    :::vim
    Bundle 'klen/python-mode'
    Bundle 'git://github.com/davidhalter/jedi-vim'

###我的配置文件

下面是我配置文件里装插件的部分

    :::vim
    "Vundle vim的插件管理程序{

    """"""""
    filetype off
    set rtp+=~/.vim/bundle/vundle/
    call vundle#rc()
    " 让Bundle管理Vundle
    Bundle 'gmarik/vundle'
    """"""""以上为必须的部分


    "安装的插件从这里开始：


    "强大的状态栏
    Bundle 'Lokaltog/powerline',{'rtp':'powerline/bindings/vim/'}
    "设置字体
    set guifont=DejaVu\ Sans\ Mono\ for\ Powerline\ 9
    "总是显示
    set laststatus=2


    "自动配对括号引号
    Bundle 'AutoClose'

    "显示树状文件目录
    Bundle 'The-NERD-tree'
    "定义一下启动nerd tree的热键，我用的是Ctrl+n
    map <C-n> :NERDTreeToggle<CR>
    "自动打开nerdtree
    "autocmd vimenter * NERDTree
    "在只剩一个nerdtree的情况下关闭窗口
    autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTreeType") && b:NERDTreeType == "primary") | q | endif


    "python的自动补全插件
    Bundle 'git://github.com/davidhalter/jedi-vim'
    "设置补全热键
    let g:jedi#completions_command = "<C-l>"


    "python-mode插件
    Bundle 'klen/python-mode'
    "缩进
    let g:pymode_indent = 1 
    "折叠
    let g:pymode_folding = 1
    "快速运动
    let g:pymode_motion = 1
    "文档
    let g:pymode_doc = 1
    let g:pymode_doc_key = 'K'
    "支持虚拟环境
    let g:pymode_virtualenv = 1
    "运行程序
    let g:pymode_run = 1
    let g:pymode_run_bind = '<F8>'
    "断点
    let g:pymode_breakpoint = 1
    let g:pymode_breakpoint_key = 'b'
    "拼写检查
    let g:pymode_lint = 1
    let g:pymode_lint_on_write = 1
    let g:pymode_lint_message = 1
    let g:pymode_lint_checker = ['pyflakes','pep8']
    "自动补全，这里没有启用，和前面的jide-vim冲突
    ""map <C-l> <C-Space>
    let g:pymode_rope = 0 
    ""let g:pymode_rope_completion_bind = '<C-Space>'
    "高亮
    let g:pymode_syntax = 1
    let g:pymode_syntax_all = 1
    let g:pymode_syntax_indent_errors = g:pymode_syntax_all
    let g:pymode_syntax_space_errors = g:pymode_syntax_all


    "markdown高亮插件
    Bundle 'plasticboy/vim-markdown'


    filetype plugin indent on     " 必须的
    " Brief help
    " :BundleList          - list configured bundles
    " :BundleInstall(!)    - install (update) bundles
    " :BundleSearch(!) foo - search (or refresh cache first) for foo
    " :BundleClean(!)      - confirm (or auto-approve) removal of unused bundles
    "
    " see :h vundle for more details or wiki for FAQ
    " NOTE: comments after Bundle commands are not allowed.
    "}

