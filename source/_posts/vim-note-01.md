---
title: Vim-note-01
date: 2022-05-09 10:02:50
tags: vim
---

## 第 1 章 起步



### 参数

```bash
vim --version  #查看vim版本
vim +%s/abc/def/g number.txt #打开number.txt,并立即把abc替换成def
vim -c %s/abc/def/gnumber.txt #同上
```



### 同时打开多个窗口

使用字母 `o` 和 `O` 选项打开后分别显示水平和垂直分割的窗口

```bash
vim -o2 txt1 txt2 #水平分割
vim -O3 txt1 txt2 txt3 #垂直分割
```



### 挂起

同执行 shell 命令一样，按组合键 `Ctrl-z` 挂起，也可以用 `:stop` 或 `:suspend`
查看哪些挂起任务,则用 `jobs` 命令查看后台任务列表
`fg` 命令从后台挂起状态返回前台，默认返回 `+` 状态的挂起任务，可以用 `fg %3` 来指定回到任务3



### 聪明启动 Vim

```bash
ls -l | vim -  #通过管道，把内容输出到新建的vim文件中。 后面必须加一个减号。再用:w name 保存文件名。
```





