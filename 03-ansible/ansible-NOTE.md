# ansible

## Inventory

### 概念

- Inventory 清单

> Ansible works against multiple systems in your infrastructure at the same time. It does this by selecting portions of systems listed in Ansible’s inventory, which defaults to being saved in the location `/etc/ansible/hosts`. You can specify a different inventory file using the `-i <path>` option on the command line.

Ansible 能够同时操控多台机器。这些机器记录在 Inventory（清单） 上，清单的默认文件是 `/etc/ansible/hosts` 我们也可以通过 `-i` 参数指定另外的清单文件。

## 安装

https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html

yum install ansible

### 配置示例

```ini
# vim /etc/ansible/host 配置主机
[sunsea] # 同一类型主机可以凑到一块，去一个名字
sunsea0
sunsea1
sunsea2
sunsea3
```

ssh 变量的配置 https://serverfault.com/questions/628989/how-to-set-default-ansible-username-password-for-ssh-connection

跳过 ssh figureprint 验证:

https://stackoverflow.com/questions/32297456/how-to-ignore-ansible-ssh-authenticity-checking

在 `/etc/ansible/ansible.cfg` 文件修改配置

```ini
[defaults]
host_key_checking = False
```

## playbook 使用

`ansible-playbook xxx.yml`

## 各模块使用说明

### shell

- `ansible all -m ping` 第一条命令测试服务是否正常

#### 给主机新增用户

- `ansible all -m shell -a "useradd -m yeming"` 主机批量添加用户 yeming
- `ansible all -m shell -a "echo xxx|passwd --stdin yeming" `修改用户密码
- `ansible all -m shell -a "ls /home/" 与 "cat /etc/passwd"` 检查用户是否添加成功

### yum

`ansible sunsea -m yum -a "name=python-pip"` 安装 pip

```yaml
# playbook 示例
- hosts: sunsea
  tasks:
    - name: install requirement
      yum:
        name: 
          - wget
          - git
          - zsh
          - net-tools 
        state: present
```

### lineinfile

https://hoxis.github.io/ansible-files-modules-lineinfile.html

删除行 state=absent


```shell
# 文件末尾追加一行
ansible sunsea -m lineinfile -a "dest=/etc/vimrc state=present line='set nu'"
```

```yaml
# playbook 示例
- hosts: sunsea
  tasks:
    - name: modify file	
      lineinfile:
        path: /root/test_ansible.txt # 指定要修改的文件
        regexp: '^PROMPT\+=' # 正则匹配行
        line: TESTLINE=TEST_FROM_ANSIBLE_PLAYBOOK # 替换为...
```

### copy

`ansible sunsea -m copy -a "src=/User/yeming/Desktop/test_ansible.txt dest=/root/test_ansible.txt"`

```yaml
# playbook 示例
- hosts: sunsea
  tasks:
    - name: Copy file to remote
      copy:
        src: /Users/yanyeming/ansible_ELK/test_ansible2.txt # 源路径
        dest: /root/test_ansible2.txt # 目标路径
```

### 综合应用

```yaml
- hosts: sunsea
  tasks:
    - name: Download cheat
      git:
        repo: 'https://github.com/chrisallenlane/cheat.git'
        dest: /root/cheat/

    - name: install cheat
      shell: python setup.py install
      args:
        chdir: /root/cheat/
```
