---
layout: post
title: 近期喜欢的好看的主题-one dark
categories: Vim
description: 近期喜欢的好看的主题-one dark。
keywords: 主题, onedark
---

# 挺好看的编程主题

我最近比较喜欢的主题和字体。因为它太好看了，所以不但要用在`VSCode`上，还要用在`vim`上，和其他能用的地方。

## 颜色主题——One Dark

颜色主题的名称基干是`One Dark`，应该最早是从`Atom`发展出来的，所以叫`Atom One Dark`，后来产生了变种如`One Dark Pro`，`One Dark Pro Vivid`等等，在`VSCode`里可以选择[One Dark Pro](https://binaryify.github.io/OneDark-Pro/#/)。

![vsc_onrdark.png](/images/posts/vim/vsc_onrdark.png)

## 在 VSCode 里安装

安装方法很简单，直接在插件标签里输入`one dark`搜索就可以安装了。安装好之后怎么启用呢？

![](/images/posts/vim/vsc_enable_onedark.png)

在左上角菜单的首选项里找到颜色主题，就可以启用了。

## 在 vim里安装

这么漂亮的主题，不用来装在`vim`里就可惜了。我们得做三件事：

第一，在`~`目录下建一个子目录`.vim`，然后在`.vim`目录下再建一个子目录`colors`，然后在`colors`目录下建一个文件`onedark.vim`，把[这个文件](https://github.com/joshdick/onedark.vim/blob/master/colors/onedark.vim)的内容拷进去。 第二，在`.vim`目录下再建一个子目录`autoload`，然后在`autoload`下建一个文件`onedark.vim`，然后把[这个文件](https://github.com/joshdick/onedark.vim/blob/master/autoload/onedark.vim)的内容拷进去。 第三，在`~`目录下建一个文件`.vimrc`，把下面的内容拷进去：

```bash
if (empty($TMUX))
  if (has("nvim"))
    let $NVIM_TUI_ENABLE_TRUE_COLOR=1
  endif
  if (has("termguicolors"))
    set termguicolors
  endif
endif

syntax on
colorscheme onedark
filetype indent on
set smartindent
set expandtab
set shiftwidth=4
set paste
```

`PS.` 如果你的`vi`不等于`vim`，你还需要在`~/.config/fish/config.fish`里写上`alias vi=vim`，这样你的`vi`就等于`vim`了。

## Fira Code 字体

是不是已经足够漂亮了呢？也不尽然，我们的大杀器出场！[Fira Code](https://github.com/tonsky/FiraCode)，这是在`github`上高达`27,000`多颗星的字体，字体星数仅次于著名的`Font Awesome`，尤其是这个`&`符号有点意思。

不同于颜色主题需要在两端安装，这个`Fira Code`字体只需要在`linux`上安装就好了，因为`Terminal`只能使用客户端字体，所以不需要在服务器安装。



## 祝你每天编程好心情！