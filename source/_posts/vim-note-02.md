---
title: Vim-note-02
date: 2022-05-10 14:17:08
tags: vim
---

## 第 2 章 缓冲区（Buffers），窗口（Windows）和选项卡（Tabs）



### Buffers

1. buffer 是一个内存中的一个空间，每当 vim 打开一个文件，该文件的数据就与一个 buffer 绑定，即，当您在 vim 中打开3个文件，就有3个 buffers
2. 在`~/.vimrc`中添加`set hidden` 保存，然后激活它(在 vimrc 文件中运行  `:source %` )，可以在当前 buffer 未保存时切换到另一个 buffer，否则会提示保存错误 `E37: No write since last change (add ! to override)`
3. 切换 buffer 有几种方法：
   - `:bnext`  切换至下一个 buffer（`:bprevious` 切换至前一个  buffer），可用缩写 `:bn`
   - `:buffer`  + 文件名。（按下 `<Tab>` 键 Vim 会自动补全文件名）。
   - `:buffer`  + `n`, n 是  buffer 的编号。比如，输入`:buffer 2`将使您切换到 buffer #2。
   - 按下 `Ctrl-O` 将跳转至跳转列表中旧的位置，对应的，按下 `Ctrl-I` 将跳转至跳转列表中新的位置。这并不是属于 buffer 的特有方法，但它可以用来在不同的 buffers 中跳转。我将在第 5 章详细讲述关于跳转的知识。
   - 按下 `Ctrl-^` 跳转至先前编辑过的 buffer。
4. 删除 buffer 可以输入 `:bdelete`。这条命令也可以接收一个 buffer 编号（`:bdelete 3`将删除 buffer #3）或一个文件名（`:bdelete` 然后按 `<Tab> `自动补全文件名），感觉没什么用，使用 `Ctrl-i/o` 仍然可以切换出来。



### 退出 Vim

如果有多个 buffer，可以使用 `:qall`、`:qall!`、`:wqall`，这跟 `:wq` 一样很好理解，测试 all 可以缩写成 a.



### Windows

窗口、视图，及我们看到的 vim 界面。

在 vim 编辑界面，使用 `:split filename`  或者  `:vsplit filename` 、`:new filename` 可以在当前页面分割窗口，同时显示多个 buffer。这个最终的显示效果跟同时打开多个窗口是一样的。

```bash
vim -o2 txt1 txt2 #水平分割
vim -O3 txt1 txt2 txt3 #垂直分割
```

在分割的窗口之间切换编辑，可以使用 `Ctrl-W`  前缀键，再按 `hjkl` 切换左上下右。

在 vim 编辑界面，也可以直接创建新窗口：

```bash
Ctrl-W V    打开一个新的垂直分割的窗口
Ctrl-W S    打开一个新的水平分割的窗口
Ctrl-W C    关闭一个窗口
Ctrl-W O    除了当前窗口，关闭所有其他的窗口
```



### Tabs

Tabs是windows的集合，就像窗口的布局，可以看成是浏览器的多标签（多个页面）。

 在vim编辑界面，也可以直接创建新Tabs：

```bash
:tabnew file.txt    #在tab中打开一个文件
:tabclose           #关闭当前tab
gt                  #切换至下一个tab
gT                  #切换至上一个tab
```

也可以直接打开多 Tabs：

```bash
vim -p txt1 txt2 txt3
```

多个 tabs 的好处是，你可以让第一个 vim 页面布局水平显示 2 个窗口，第二个 vim 页面布局垂直显示 3 个窗口。



### 三维移动

如果把 windows 比喻是 x-y 轴，buffer 比喻是 z 轴，光标在 x-y 轴上下左右移动，在 z 轴前后移动，整体构成一种三维移动感。



### 适合自己的工作流程

1. 先用 buffer 存储所有可能需要修改的文件。 Vim 就算打开很多 buffer，始终只会看到 1 个 buffer（假设只有1个 window），这样可以集中注意力，也能快速切换 buffer。
2. 当对比文档、读文档、追踪代码流的时候，保持屏幕不操过 3 个 window。
3. 使用tmux工具来代替 tabs
