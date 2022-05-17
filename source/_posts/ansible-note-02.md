---
title: Ansible-note-02
date: 2022-05-15 23:24:31
tags: Ansible
---

## 第 2 章 部署 Ansible

### Ansible 配置文件

#### 4 种配置文件优先级

Ansible 配置文件按优先级匹配，<u>**匹配后会忽视其余配置**</u>：

1. 变量指定，优先级别最高，**不推荐，不常用。**

   ```bash
   export ANSIBLE=CONFIG=/tmp/ansible.cfg 	
   ```

2. 工作目录配置文件，您当前执行命令的文件夹， `pwd` 查看，第二优先级，**推荐，常用。**

3. 家目录配置文件，第三优先级，**备用目录**

4. 默认配置文件，`/etc/ansible/ansible.cfg`，**一般不使用**

#### 常规 `ansible` 配置

配置示例

```bash
[defaults]
#指定清单文件的位置
inventory = ./inventory
#要在受管理主机上登陆的用户名，如果未指定，则使用当前用户名
remote_user = username
#是否提示输入SSH密码，如果使用SSH公钥验证，则可以是false
ask_pass = false

[privilege_escalation]
#连接后是否自动在受管理主机上切换用户（通常切换为root），这也可以通过play来指定
become = true
#如何切换用户（通常为sudo，这也是默认设置，但也可选择su）
becom_method = sudo
#要在受管主机上切换的用户（通常为root，也是默认值）
becom_user = root
#是否要为become_method提示输入密码，默认为false
becom_ask_pass = false
```



### Ansible 管理清单文件

1. `inventory` 文件就是简单文本，使用 ini 格式记录受控主机清单（文件名不是必须 inventory ）。
2. `inventory` 文件可以使用简单范围语法 `[START:END]` 来指定字母或者数字的范围。
3. `inventory` 文件可以在 `ansible.cfg` 定义位置，也可以使用 `-i` 参数指定。

```bash
192.168.1.2		#可以直接写ip地址
servera		#也可以直接写主机名

[myself] 		#定义组名，不能包含空格
localhost   #包含的主机

[intranetweb]  
servera		
serverb

[internetweb]
serverc
serverd

[test]
server[b:d]			#指serverb、serverc、serverd


[web:children]		#定义嵌套组，必须以:children结尾
intranetweb				#嵌套的组名
internetweb
```



### Ansible 命令行选项

| 配置文件指令      | 命令行选项            |
| ----------------- | --------------------- |
| `inventory`       | -i                    |
| `remote_user`     | -u                    |
| `become`          | --become、-b          |
| `become_method`   | --become-method       |
| `become_user`     | --become-user         |
| `become_ask_pass` | --ask-become-pass、-K |



### Ansible adhoc

> adhoc 是概念性说法，指 `ansible` 命令行临时执行的方式，一般不需要特别保存这些执行的命令，都可以叫做adhoc，理解adhoc有助于理解后面 playbook。

adhoc 命令格式是：

```bash
ansible host-pattern -m module [-a 'module arguments'] [-i inventory]
```

`host-pattern` 指主机或主机组名，在 `inventory` 里定义。

`module` 指 `Ansible` 管理模块，可以用 `ansible-doc -l `查看所有可用模块，未指定时默认时command模块。

`-a 'module arguments'` 指使用模块传递到受控主机的参数。

`-i inventory` 指定 `inventory` 文件，其余见命令行选项表。

`-o` 以单行格式显示 `Ansible` 临时命令的输出

