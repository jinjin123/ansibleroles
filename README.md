# 系统环境
os:  `Centos 6.7 X64`

python: `2.6.6`

# 安装ansible和git

```bash
[root@node ~]# yum install -y ansible git
[root@node ~]# ansible --version
ansible 2.2.1.0
  config file = /etc/ansible/ansible.cfg
  configured module search path = Default w/o overrides
```

## 设置ansible
关闭主机ssh known_hosts检查
配置文件：/etc/ansible/ansible.cfg
```vim
 59 host_key_checking = False
```

## 使用git 克隆ansible role
```bash
git clone   to /etc/ansible/roles

Initialized empty Git repository in /etc/ansible/roles/.git/
remote: Counting objects: 165, done.
remote: Compressing objects: 100% (95/95), done.
remote: Total 165 (delta 37), reused 152 (delta 24), pack-reused 0
Receiving objects: 100% (165/165), 26.01 KiB | 19 KiB/s, done.
Resolving deltas: 100% (37/37), done.
[root@node roles]# ll /etc/ansible/roles/
总用量 36
drwxr-xr-x 5 root root 4096 3月  11 11:54 iptables
drwxr-xr-x 5 root root 4096 3月  11 11:54 java
drwxr-xr-x 4 root root 4096 3月  11 11:54 mvn
drwxr-xr-x 5 root root 4096 3月  11 11:54 nodejs
drwxr-xr-x 5 root root 4096 3月  11 11:54 os-init
drwxr-xr-x 5 root root 4096 3月  11 11:54 python2.7
drwxr-xr-x 4 root root 4096 3月  11 11:54 repo-epel
drwxr-xr-x 4 root root 4096 3月  11 11:54 ruby
drwxr-xr-x 5 root root 4096 3月  11 11:54 supervisor
[root@node roles]# 
```

## 添加主机清单
这里以192.168.77.130主机为例，并将其设为node2组内。
```
#/etc/ansible/hosts
[node2]
192.168.77.130 ansible_ssh_pass=123456
```

## 查看role的README.md文档

```
# Ansible Role: python2.7

Centos 6.7版本中的python2.6升级到2.7

## 要求

此角色仅在RHEL及其衍生产品上运行。

## 测试环境

ansible `2.2.1.0`
os `Centos 6.7 X64`

## 角色变量
	software_files_path: "/opt/software/"
	software_install_path: "/usr/local"

	python_version: "2.7.13"

	python_file: "Python-{{ python_version }}.tgz"
	python_file_path: "{{ software_files_path }}/{{ python_file }}"
	python_file_url: "http://www.python.org/ftp/python/{{ python_version }}/Python-{{ python_version }}.tgz"

	pip_source_url: "https://pypi.tuna.tsinghua.edu.cn/simple"
	change_pip_source: true


## 依赖

没有


## Example Playbook

    - hosts: servers
      roles:
        - python2.7
		
	- hosts: server
      roles:
        - { role: python2.7, python_version: "2.7.13"}
```

- 上诉说明了此role在哪个环境下正常运行，其他环境暂不保证运行成功。
- 可以自定义角色变量，达到自己想要的结果。比如想要安装python2.7.12的版本，可以在playbook中定义`python_version: "2.7.12"`,就可以安装2.7.12版本的python了。
- 如果文章中有依赖，必须先安装依赖，才能运行此role。
- 在编写Playbook时也可以参考上列的Playbook例子。

## 编写playbook

这里以python2.7角色为例，将为node2组内的所有主机安装python2.7服务。
根据自身情况设置相应的变量，这里设置python的版本为2.7.14。
文件路径：/etc/ansible/roles/python2.7.yml

```yml
---

- hosts: node2
  
  roles:
    - { role: python2.7, python_version: "2.7.14"}
```

## 执行playbook
```bash
[root@node ansible]# ansible-playbook python2.7.yml 

PLAY [node2] *******************************************************************

TASK [setup] *******************************************************************
ok: [192.168.77.130]

TASK [python2.7 : Copy python file to agent.] **********************************
ok: [192.168.77.130]

TASK [python2.7 : Create software files .] *************************************
ok: [192.168.77.130]

TASK [python2.7 : Download python file.] ***************************************
changed: [192.168.77.130]

TASK [python2.7 : Check if python remote soft link is already configured.] *****
ok: [192.168.77.130]

TASK [python2.7 : Check if python remote soft old link is already configured.] *
ok: [192.168.77.130]

TASK [python2.7 : Ensure packages are installed.] ******************************
ok: [192.168.77.130] => (item=[u'zlib', u'zlib-devel', u'openssl', u'openssl-devel', u'python-devel', u'gcc'])

TASK [python2.7 : Copy python file to agent.] **********************************
changed: [192.168.77.130]

TASK [python2.7 : Build python.] ***********************************************
changed: [192.168.77.130]

TASK [python2.7 : Move python old version and Create python dir soft link.] ****
changed: [192.168.77.130]

TASK [python2.7 : Replace file python env path.] *******************************
changed: [192.168.77.130] => (item=/usr/sbin/iotop)
changed: [192.168.77.130] => (item=/usr/bin/yum)

TASK [python2.7 : Check if pip is already configured.] *************************
ok: [192.168.77.130]

TASK [python2.7 : Install python2.7 pip.] **************************************
changed: [192.168.77.130]
 [WARNING]: Consider using get_url or uri module rather than running curl


TASK [python2.7 : Create pip config dir.] **************************************
changed: [192.168.77.130]

TASK [python2.7 : Change pip source.] ******************************************
changed: [192.168.77.130]

PLAY RECAP *********************************************************************
192.168.77.130             : ok=15   changed=8    unreachable=0    failed=0 
```
## 查看主机是否安装了python2.7版本
使用ad-hoc方式查看
```bash
[root@node ansible]# ansible node2 -m shell -a 'python -V'
192.168.77.130 | SUCCESS | rc=0 >>
Python 2.7.12
```
在主机上查看
```bash
[root@node2 ~]# python -V
Python 2.7.12
```
# facts info comment
```
ansible_all_ipv4_addresses
ansible_distribution_version
ansible_distribution
ansible_kernel
ansible_memory_mb  内存 int(257345 / 1024)   GB
ansible_mounts   挂载盘  int(502752256000 / 1024 /1024/1024) GB
ansible_nodename   hostname
ansible_processor_cores  物理核
ansible_processor_count  CPU数量
ansible_system_vendor  供应商


windows disk  bug
ANSIBLE_LOAD_CALLBACK_PLUGINS=1 ANSIBLE_STDOUT_CALLBACK=actionable ansible fetioc  -m setup  --tree ioc/
ANSIBLE_LOAD_CALLBACK_PLUGINS=1 ANSIBLE_STDOUT_CALLBACK=actionable ansible fetall  -m ping
- ansible ip  -m setup -a "filter=ansible_devices" ， 用ansible_devices里面的就只有本机的硬盘
- 还有硬盘这里，最好使用总的大小，系统盘总大小+数据盘总大小。方便等保统计
发布到rancher，对比一下镜像地址，不匹配就终止？

###cmdb
python -m pip install ansible-cmdb -i https://pypi.tuna.tsinghua.edu.cn/simple

ANSIBLE_LOAD_CALLBACK_PLUGINS=1 ANSIBLE_STDOUT_CALLBACK=actionable ansible fetall -m ping

ansible localhost  -m setup  --tree out/
ansible-cmdb -t json out/ > a.json
```
