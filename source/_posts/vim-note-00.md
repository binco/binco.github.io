---
title: Vim-note-00
date: 2022-05-04 16:11:39
tags: vim
---

学习连接 https://github.com/wsdjeg/Learn-Vim_zh_cn

## 第 0 章 请先阅读

1. 本章提到 vimtutot 是一个很好的学习途径。这个命令可以打开一个 Vim 教学指南，我从头学过一遍，知道了可以使用`:!`在不退出 vim 的情况下运行shell命令，推荐大家都使用一次

2. 还提到 Vimscript 脚本语言，“您当然可以继续使用 Vim 而不了解任何 Vimscript 知识，但了解它可以使您更加擅长Vim。”

3. 锻炼肌肉记忆，在实践中探索，能够本能、自然地使用命令。

4. 作者提前说明，在本教程中对于经常使用的命令使用简写，对于不经常使用的命令使用全程。`:join可以简写成:j`

5. vimrc 选项，可以看作一个配置文件，需要`:source %`使其生效。它会使 vim 配置永久生效，而不是临时生效。这个跟我最近刚知道的 ssh_config 配置文件相似，感觉是个好东西。

   ```bash
   #这里收集一些自用的vimrc文件配置
   
   set nocompatible  #开启所有vim扩展
   set number        #开启行号
   set hidden        #开启缓冲区快速切换，见vim-note-02
   syntax on         #开启语法高亮
   ```

   

6. 如果启用`compatible`选项，许多Vim特有的功能将会被禁止，所以在vimrc文件中添加`set nocompatible`

