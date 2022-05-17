---
title: Vim-note-03
date: 2022-05-12 15:14:28
tags: vim
---

## 第 3 章 打开和搜索文件

如何在 Vim 中快速搜索？

1. 不依赖插件搜索
2. 使用fzf插件搜索

### 打开和编辑文件

要在 Vim 中打开一个文件，可以使用 `:edit`

```bash
:edit file.txt
```

如果文件存在，就会打开，如果不存在就会创建一个新的 buffer 名为 file.txt。

`:edit` 支持Tab补全、通配符及递归搜索，也可以指定目录，列出目录所有文件。

### 使用 find 命令搜索文件

可以使用 `:find` 命令搜索文件，比如：

```bash
:find file.txt
:find app/controllers/users_controller.rb
```

`:find `命令也支持命令补全。

### Find 和 Path

两者的区别在于，`:find`命令根据 `path` 选项配置的路径查找文件，而 `:edit` 不会。

```
:set path?
```

默认情况下，您的 `path `内容很可能是这样的：

```
path=.,/usr/include,,
```

- `.` 意思是在当前文件所在目录下搜索。(译者注：注意不是命令行输入pwd返回的当前目录，而是 **当前所打开的文件** 所在的目录)
- `,` means to search in the current directory. (译者注：此处貌似作者有点小错误，逗号`,`应该是表示路径之间的分割符。连续的两个`,,`（两个逗号之间为空）才表示当前目录)
- `/usr/include` 表示在C编译器头文件目录下搜索，可以忽略不设置。

可以将 `set path+={您需要添加的目录}`添加到您的 vimrc 文件中，这样 find 就能搜索设置的目录。

### 使用grep命令在文件中搜索

**<u>在 Vim 内部</u>**查找文件中内容的方法， Vim 提供两种：

- 内部 grep ， `:vim`
- 外部 grep ， `:grep`

`:vim `有以下语法：

```bash
:vim /pattern/ file  #先导航到第一个结果
:copen		#再打开quickfix窗口查看所有搜索结果
:q		#退出
```

- `/pattern/` 是您要搜索的内容的正则表达式。
- `file` 是文件参数。您可以传入多个参数。Vim将在文件中搜索所有匹配正则表达式的内容。类似于`:find`，您可以传入*和**通配符。
- *如果只搜索文件本身内部，大可直接 `:/` 搜索即可，不必使用gre命令搜索。*

`:grep` 有一下语法：

```bash
:grep -R "lunch" filename		#使用方法同终端 grep 命令一致
```

注意它遵循的是终端 grep 的语法`"pattern"`，它同样使用 'quickfix' 来显示所有的匹配结果。

### 用 Netrw 浏览文件

使用 Vim 打开一个目录就会进入 Netrw 界面。

在 Vim 内部，可以使用 `:edit 目录` 来打开 Netrw 界面。

个人觉得 Netrw 没什么便利性，一笔带过。

