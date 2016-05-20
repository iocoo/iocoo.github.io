---
layout: post
title: TextWrangler和Vim配置GoLang 语法高亮
date: 2016-05-20 10:50:00 +0800
cacategory: golang
author: GIGI WANG
tags: golang
---

<h3>OSX 下配置VIM语法高亮</h3>
 Vim原生不支持golang语法高亮，因此要借助插件。功能强大又好用的插件有很多，这里就只简单介绍一种。
 vim-go插件,github在[这里](https://github.com/fatih/vim-go).
 
 我们来使用vim-plug[🌺 Minimalist Vim Plugin Manager]来安装，安装方法：
 
 -1. 下载plug.vim .
 并放置到~/.vim/autoload 目录下,点击[这里下载](https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim).
   也可以直接使用curl下载。
   <pre class="brush:bash">curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim</pre>
    
 -2. 编辑~/.vimrc 文件,添加以下内容:
 <pre class="brush:bash">
 call plug#begin('~/.vim/plugged')
 Plug 'fatih/vim-go', { 'tag': '*' }
 call plug#end()
 </pre>
 -3. 打开vim，输入命令:
  <pre class="brush:bash">
  :PlugInstall
  </pre> 
 -4. 完成，vim打开一个*.go 文件看看。
 
<h3>配置TextWrangler或BBEdit</h3>
BBEdit官方网站给出了配置方法，当然测试了免费的TextWrangler ，也是可行的。
官方给出了好多可用的语言供选择，地址在[这里](http://www.barebones.com/support/bbedit/plugin_library.html).可供选择的包括了Erlang,Haskell,Go等等..
>[http://www.barebones.com/support/bbedit/plugin_library.html](http://www.barebones.com/support/bbedit/plugin_library.html)

 -1. 在该页面找到Go下载地址下载，或点击这里 [直接下载](http://pine.barebones.com/extensions/Go.plist.zip)。
 -2. 下载的文件Go.plist.zip 解压成Go.plist。
 -3. 将该文件放置到```/Users/{USERNAME}/Library/Application Support/TextWrangler/Language Modules```, 重新打开TextWrangler。一切OK。
 
 