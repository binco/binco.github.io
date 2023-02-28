---
title: ansible-对文件内容的操作
date: 2022-09-10 07:43:54
tags: ansible
---

本文转载：https://www.cnblogs.com/shoufu/p/14764646.html

# ansible 对文件内容的操作

## ansible lineinfile

### 简介

`lineinfile`该模块是操作文件中的每一行内容，他是按照行为单位的，和下面的`replace`模块并不冲突。

### 修改匹配行，如果不存在就会添加

```
tasks:
- name: Ensure SELinux is set to enforcing mode
  lineinfile:
    path: /etc/selinux/config
    regexp: '^SELINUX='
    line: SELINUX=enforcing
```

把 `SELINUX=`这个开头的行直接替换成`SELINUX=enforcing`不管后面是什么，直接替换整行内容。

### 删除文件中的行

```
- name:  确保sudoers配置中没有wheel组。
  lineinfile:
    path: /etc/sudoers
    state: absent
    regexp: '^%wheel'
```

### 在匹配行前添加一行内容，并确保插入成功

```
- name: Ensure the default Apache port is 8080
  lineinfile:
    path: /etc/httpd/conf/httpd.conf
    regexp: '^Listen '						//确保添加完成
    insertafter: '^#Listen '			//要在哪一行前面添加
    line: Listen 8080							//添加的内容
```

### 在匹配行后添加一行内容，并确保插入成功

```
- name: Ensure we have our own comment added to /etc/services
  lineinfile:
    path: /etc/services
    regexp: '^# port for http'					//确保添加完成
    insertbefore: '^www.*80/tcp'				//要在哪一行后面添加
    line: '# port for http by default'	//添加的内容是什么
```

### 修改文件并更改权限

```
- name: Replace a localhost entry with our own
  lineinfile:
    path: /etc/hosts
    regexp: '^127\.0\.0\.1'
    line: 127.0.0.1 localhost
    owner: root
    group: root
    mode: '0644'
```

### 文件存在就添加一行内容，如果内容存在就不会添加（多次执行不会重复添加）

```
- name: add a line
  lineinfile:
    dest: /etc/hosts
    line: '10.1.1.1 zhangshoufu.com'
```

## ansible replace（非核心模块）

### 介绍

`replace`模块可以根据我们指定的正则表达式替换匹配到的字符串，文件中所有被匹配到的字符串都会被替换，和`lineinfile`不同的地方是`replace`只会替换正则表达式匹配到的内容，而`lineinfile`是替换正则表达式匹配到行的内容。

### 常用参数

- path: 文件路径，我们要替换那个文件内的内容，必须
- regexp：正则表达式，必要参数
- replace： 替换成的内容

### 替换文件内容

```
tasks:
- name: '替换zsf 字符串为zhangshoufu'
  replace:
    path: /tmp/test.txt
    regexp: 'zsf'
    replace: 'zhangshoufu'
```

### 注释 Apache 配置文件`/etc/apache2/sites-available/default.conf`中`NameVirtualHost [*]`行之后的所有内容：

```
- name: Replace after the expression till the end of the file (requires Ansible >= 2.4)
  replace:
    path: /etc/apache2/sites-available/default.conf
    after: 'NameVirtualHost [*]'
    regexp: '^(.+)$'
    replace: '# \1'
```

### 注释 Apache 配置文件`/etc/apache2/sites-available/default.conf`中`# live site config`行之前的所有内容：

```
- name: Replace before the expression till the begin of the file (requires Ansible >= 2.4)
  replace:
    path: /etc/apache2/sites-available/default.conf
    before: '# live site config'
    regexp: '^(.+)$'
    replace: '# \1'
```

### 注释 Apache 配置文件`/etc/apache2/sites-available/default.conf`中`<VirtualHost [*]>`行和`</VirtualHost>`行之间的内容：

```
# Prior to Ansible 2.7.10, using before and after in combination did the opposite of what was intended.
# see https://github.com/ansible/ansible/issues/31354 for details.
- name: Replace between the expressions (requires Ansible >= 2.4)
  replace:
    path: /etc/apache2/sites-available/default.conf
    after: '<VirtualHost [*]>'
    before: '</VirtualHost>'
    regexp: '^(.+)$'
    replace: '# \1'
```

### 删除`jdoe`用户 SSH 的`known_hosts`文件中的`old.host.name`及之后的空行，同时修改文件属性和权限：

```
- name: Supports common file attributes
  replace:
    path: /home/jdoe/.ssh/known_hosts
    regexp: '^old\.host\.name[^\n]*\n'
    owner: jdoe
    group: jdoe
    mode: '0644'
```

### 修改`/etc/apache/ports`文件并校验：

```
- name: Supports a validate command
  replace:
    path: /etc/apache/ports
    regexp: '^(NameVirtualHost|Listen)\s+80\s*$'
    replace: '\1 127.0.0.1:8080'
    validate: '/usr/sbin/apache2ctl -f %s -t'
```

参考：https://www.ityoudao.cn/posts/ansible-modules-replace/

## ansible blockinfile

### 简介

和`lineinfile`有点类似，他可以帮助我们在文件中插入一段文本。

### 常用参数

- path: 要操作的文件名称

- state：present 确保段落存在，absent 确保段落不存在，默认值为 present，会将指定的一段文本插入到文件中，乳沟文件中已经存在标记的文本，会重新更改；absent 删除对应的段落

- marker：才操作的段落中添加标记信息。默认值为"# {mark} ANSIBLE MANAGED BLOCK"

  > 我们想要在指定文件中插入一段文本，Ansible 会自动为这段文本添加两个标记，一个开始标记，一个结束标记，默认情况下，开始标记为`# BEGIN ANSIBLE MANAGED BLOCK`，结束标记为`# END ANSIBLE MANAGED BLOCK`。
  >
  > `{mark}`变量会自动被替换成开始标记中的`marker_begin`和结束标记中的`marker_end`，如果使用没有`{mark}`变量的自定义标记，可能会导致重复插入。

- marker_begin: 设置 marker 参数的开始标记中的 {mark}变量，默认值为“BEGIN”

- marker_end： 设置 marker 参数的结束标记中的 {mark}变量，默认值为“END”

- block： 指定一段要操作的文本，如果没有 block 参数或者参数的值为空，则移除文本块，等同于 state=absent.

- insertafter: 在匹配后添加。

  > 值为`EOF`或者正则表达式，默认值为`EOF`，表示`End Of File`，插入到文件的末尾。
  >
  > 如果设置为正则表达式，默认将文本插入到正则表达式匹配的最后一行之后。
  >
  > 如果设置为正则表达式，但是没有匹配到任何行，则插入到文件末尾。

- insertbefore: 在匹配前添加

  > 插入段落（state=present）时使用。
  >
  > 值为`BOF`或者正则表达式，默认值为`BOF`，表示`Begin Of File`，插入到文件的开头。
  >
  > 如果设置为正则表达式，默认将文本插入到正则表达式匹配的最后一行之前。
  >
  > 如果设置为正则表达式，但是没有匹配到任何行，则插入到文件开头。

- backup： 操作前是否备份

### 在文件最后添加多行

```
tasks:
- name: 'add hosts'
  blockinfile:
    path: /etc/hosts
    block: |
      10.1.1.1 www.zhangshoufu.com
      10.2.2.2 test.zhangshoufu.com
```

### 备份`/etc/ssh/ssh_config`文件，并在文件末尾插入`./local/ssh_config`文件的内容，最后使用`/usr/sbin/sshd -T -f /etc/ssh/ssh_config`命令校验：

```
tasks:
- name: Insert/Update configuration using a local file and validate it
  blockinfile:
    block: "{{ lookup('file', './local/ssh_config') }}"
    dest: /etc/ssh/ssh_config
    backup: yes
    validate: /usr/sbin/sshd -T -f %s
```

### 在 HTML 文件`/var/www/html/index.html`的`"<body>"`之后插入自定义标记的内容：

```
tasks:
- name: Insert/Update HTML surrounded by custom markers after <body> line
  blockinfile:
    path: /var/www/html/index.html
    marker: "<!-- {mark} ANSIBLE MANAGED BLOCK -->"
    insertafter: "<body>"
    block: |
      <h1>Welcome to {{ ansible_hostname }}</h1>
      <p>Last updated on {{ ansible_date_time.iso8601 }}</p>
```

### 移除在 HTML 文件`/var/www/html/index.html`中的添加的内容：

```
tasks:
- name: Remove HTML as well as surrounding markers
  blockinfile:
    path: /var/www/html/index.html
    marker: "<!-- {mark} ANSIBLE MANAGED BLOCK -->"
    block: ""
```

### 在`/etc/hosts`文件中添加解析记录：

```
tasks:
- name: Add mappings to /etc/hosts
  blockinfile:
    path: /etc/hosts
    block: |
      {{ item.ip }} {{ item.name }}
    marker: "# {mark} ANSIBLE MANAGED BLOCK {{ item.name }}"
  with_items:
    - { name: host1, ip: 10.10.1.10 }
    - { name: host2, ip: 10.10.1.11 }
    - { name: host3, ip: 10.10.1.12 }
```
