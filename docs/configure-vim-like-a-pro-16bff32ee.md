# 像专业人士一样配置 Vim

> 原文：<https://levelup.gitconnected.com/configure-vim-like-a-pro-16bff32ee>

我最近开始对我的开发人员工作流更感兴趣，并决定开始迁移到 Vim 及其代码编辑的快捷方式。这是我花了几天时间整理的配置。

![](img/fa01c6b7b4bd3687e65edaae2bd069a5.png)

[西格蒙德](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

# 我的 Vim 配置

如果你不熟悉`vim`，它是一个文本编辑器，用来扩展 vi 编辑器的功能(改进的 Vi)。主要开发者和维护者是布莱姆·米勒，你可以在 GitHub 上查看他的最新进展。其中一个改进是拥有“无限”撤销功能，因此如果你改变主意，你可以回滚许多更改。

定制 vim 编辑器的标准方法是使用一个名为`.vimrc`的文件，并将其放在 Linux 或 Mac 的主目录中。在这个文件中，你可以配置默认设置，安装插件，映射每种模式下的快捷键(正常，可视，可视块，插入)。

**配置默认值**

为了提高生活质量，我喜欢在 vim 中调整一些缺省值。如果你在 vim 中输入`:help`,还有更多的东西可以查看。

```
syntax on
set nu
set relativenumber
set encoding=UTF-8
set smarttab
set smartindent
set tabstop=4 softtabstop=4
set shiftwidth=4
set expandtab
set re=0
set nowrap
set noswapfile
set incsearch
set scrolloff=8
set guicursor=
```

`syntax on`启用语法高亮显示

`nu`启用行号

`relativenumber`可显示相对行号，以便快速跳转(如 5j 或 3k)

`smartindent`将根据您的语言和其他代码缩进的位置缩进正确的数量

`tabstop, softtabstop, shiftwidth` all 设置代码块内部缩进的空格数

`expandtab`用空格替换制表符

设置正则表达式(我在 React 项目中设置这个是出于性能原因)

`nowrap`防止长文本换行

`noswapfile`防止在 vim 中打开现有文件时创建交换文件

当您使用`/`在文件中搜索时，`incsearch`将高亮显示文本

`scrolloff`向下滚动时，在底部显示 X 行

`guicursor`即使进入插入模式，也保持光标大小不变

**安装插件**

有许多不同的插件管理器，包括 vim 本身(只要你使用版本 8 或更高版本)，但我更喜欢使用 [vim-plug](https://github.com/junegunn/vim-plug) 。查看 GitHub 页面，了解如何在您的系统上安装。

要向 vim 添加插件，将下面的代码块添加到您的`.vimrc`文件中，注意插件周围的`call plug`。一旦添加完毕，你可以输入`:PlugInstall`来安装插件。如果你需要卸载，只需注释掉或删除那个插件，退出并重新打开你的`.vimrc`，再次运行`:PlugInstall`，你将被提示删除那个目录。

```
call plug#begin()
  Plug 'neoclide/coc.nvim', {'branch': 'master', 'do': 'yarn install --frozen-lockfile'}
  Plug 'junegunn/fzf', { 'do': { -> fzf#install() } }
  Plug 'junegunn/fzf.vim'
  Plug 'ryanoasis/vim-devicons'
  Plug 'scrooloose/nerdcommenter'
  Plug 'christoomey/vim-tmux-navigator'
  Plug 'Xuyuanp/nerdtree-git-plugin'
  Plug 'airblade/vim-gitgutter'
  Plug 'tiagofumo/vim-nerdtree-syntax-highlight'
  Plug 'HerringtonDarkholme/yats.vim'
  Plug 'preservim/nerdtree'
  Plug 'dense-analysis/ale'
  Plug 'vim-airline/vim-airline'
  Plug 'udalov/kotlin-vim'
call plug#end()
```

`coc.vim`允许使用已安装的语言服务器自动完成不同语言。查看 [Github 上 coc.vim](https://github.com/neoclide/coc.nvim) 的安装说明。取决于你的语言，这可能有点挑剔，我仍然试图为 Kotlin 做一个好的配置。

`fzf`和`fzf.vim`安装一个命令行模糊查找器，这样你就可以快速搜索系统中的文件。查看 [GitHub 了解更多信息](https://github.com/junegunn/fzf)。

`vim-devicons`在 vim-airline 显示屏底部显示图标，当您打开 NerdTree 时也会显示图标。注意:你需要[安装并配置一个书呆子字体](https://github.com/ryanoasis/nerd-fonts)。

`nerd-commenter`允许你用一个快捷键一次注释掉一行或多行。

`vim-tmux-navigator`允许您使用 h、j、k、l 在 tmux 和 vim 会话之间跳转

`nerdtree-git-plugin`显示文件在 NerdTree 中被编辑、删除或添加的时间

`vim-gitgutter`显示在 vim 中查看文件时的编辑、删除和添加

`vim-nerdtree-syntax-highlight`nerd tree 中的语法高亮显示

`yats.vim`是 TypeScript 的语法高亮显示

`nerdtree`允许你打开一个带有文件目录的窗口

`ale`是一个显示无效代码样式的林挺引擎

`vim-airline`增强显示 vim 模式和文件详细信息的底部栏

`kotlin-vim`为 Kotlin 做语法高亮显示。到目前为止对我来说还不是很好。

**地图键盘快捷键**

我还没有设置很多快捷方式，但是首先要改变的是 vim leader。默认情况下这是`\`，快速输入这个命令感觉有点笨拙。大多数人将他们的 vim leader 映射到空格或逗号。

```
let mapleader = " "
nnoremap <C-p> :Files<Cr>
nnoremap <Leader>f :Rg<Cr>
```

我设置的两个快捷方式是在正常模式下用于 FZF 和 Ripgrep 命令的。让我们在这里分解一下。

`n`设置模式，在这种情况下是正常模式，但您可以设置`v`为可视模式，或`i`为插入模式。

`nore`这将命令设置为非递归的。只执行一次。

`<C-p>`这是我按的键盘快捷键，Ctrl + p。

`:Files<CR>`这是要执行的命令，末尾有回车。

# 结论

希望这能让你有一个很好的 vim 配置。一旦我有了更好的带有 coc 的 Kotlin 配置，我会与大家分享，但如果你已经有了，请与大家分享！

如果你想检查所有的配置，包括我的 zshrc 和 tmux 配置，看看这里的。

如果你喜欢这篇文章，考虑[订阅 Medium](https://medium.com/@ascourter/membership) ！

如果你或你的公司有兴趣找人进行技术面试，那么请在 Twitter ( [@Exosyphon](http://twitter.com/Exosyphon) )上给我发 DM，或者访问我的[网站](https://andrewcourter.com/)。如果你喜欢这样的话题，那么你可能也会喜欢我的 Youtube 频道。如果你喜欢 3D 打印的东西，可以看看我的 Etsy 商店。祝您愉快！