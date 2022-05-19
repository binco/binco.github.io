---
title: Ansible-note-03
date: 2022-05-19 21:53:10
tags: Ansible
---

### 1、理解 Playbook

如果说 adhoc 运行一个 task 任务，那么 Playbook 则可以运行多个 task任务。

Playbook 可以包含多个 play 项目，每个 play 项目可以有多个 task 任务。

可以把 Playbook 当成一个精心准备的剧本，每个 play 就是一个个剧集，每个 task 就是一个个情节。

### 2、Playbook 格式

Playbook 是以 YAML 格式编写的文本文件，通常使用扩展名 yml 保存，Playbook 使用空格字符缩进来表示其数据结构。YAML 对于缩进的空格数量没有严格的要求，但有两个基本的规则：

> - 处于层次结构中同一级别的数据元素（例如同一列表中的项目）必须具有相同的缩进量。
> - 如果项目属于其他项目的子项，其缩进量必须大于父项。

Playbook 开头有三个破折号（---）组成，这是文档开始标记。结尾使用三个圆点（...）作为文档借宿标记，尽管在实践中这通常会省略。在两个标记之间，会以一个play列表的形式来定义 Playbook。

Playbook 结构：

```yml
---  # 文档开始标记
- A  # play 项目1
  - a  # task 任务模块1
  - b  # task 任务模块2
  - c  
- B  # play 项目2
  - a
  - b
  - c
- C  # play 项目3
  - a
  - b
  - c
...  # 文档结束标记，可忽略
```

简单示例写法：

```yml
---   #文档开始标记
- name: ababab  # 一个 play 项目 A，可选，但最好有，用于命名 play，帮助记录用途
  hosts: servera.lab.example.com   # 指定 play 项目中运行的主机或主机组，对应 inventory
  tasks:            # 指定 play 项目运行的任务模块
    - name: newbie  # 可选，但最好有，用于命名 task，帮助记录用途
      user:         # 一个 task 任务模块，通常包含参数子项 
      uid: 4000
      state: present

```

Playbook 中的 play 和 task 列出的顺序很重要，因为 Ansible 会按照顺序运行它。

### 3、运行 Playbook 

`ansible-playbook` 命令

```bash
ansible-playbook --syntax-check test.yml #验证 playbook 语法
ansible-playbook test.yml  #运行 playbook
```

### 4、执行空运行

使用 `-C` 参数

```bash
ansible-playbook -C test.yml  #空运行，仅报告直接结果，不会产生实际更改
```

