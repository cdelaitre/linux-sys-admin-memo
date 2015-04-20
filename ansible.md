# ANSIBLE

## Requirements
- **disable** `rpmforge extra repo`
- if `python-paramiko.noarch 0:1.7.6-1.el6.rfx` installed => **remove it**
```bash
yum remove python-paramiko
```

## Installation (centos6)
```bash
yum install ansible
```
```
---> Package ansible.noarch 0:1.8.4-1.el6 will be installed
---> Package python-paramiko.noarch 0:1.7.5-2.1.el6 will be installed
```
## Check
```bash
ansible --version
```
```
[WARNING]: The version of gmp you have installed has a known issue regarding
timing vulnerabilities when used with pycrypto. If possible, you should update
it (i.e. yum update gmp).

ansible 1.8.4
  configured module search path = None
```

## Client Configuration
- **Assume**
 - `hostname`:`ansibleclient`
 - `ip`:`192.168.1.2`

- **Create user**: `ansible`
```bash
useradd ansible
passwd ansible
```

- **Add ansible rights**: modify `/etc/sudoers`
```
Defaults    !requiretty
ansible         ALL=(ALL)       NOPASSWD: ALL
```

## Server Configuration
- **Assume**
 - `hostname`:`ansiblemaster`

- **Create ansible user,key and copy to client**:
```bash
useradd ansible
su - ansible
ssh-keygen -t rsa -C "ansible@ansiblemaster"
ssh-copy-id ansibleclient
```

- **Add ansible rights**: modify `/etc/sudoers`
```
Defaults    !requiretty
```

- **Add environment variables**: modify `/etc/ansible/hosts`
```
[env:vars]
ansible_ssh_user=ansible
[env]
ansibleclient
```

## Check ping: server => client
```bash
ansible env -m ping
[WARNING]: The version of gmp you have installed has a known issue regarding
timing vulnerabilities when used with pycrypto. If possible, you should update
it (i.e. yum update gmp).

192.168.1.2 | success >> {
    "changed": false,
    "ping": "pong"
}
```

## Playbook example
- webapp.yml
```
---
- name: Pull Artifactory war to tomcat7
  hosts: dev
  tasks:
  - name: clean war
    command: rm -f /produits/tomcat7/webapps/webapp.war
  - name: download war
    get_url:
      use_proxy: no
      dest: /produits/tomcat7/webapps/webapp.war
      url: http://artifactory:8081/artifactory/libs-snapshot-local/1.0.0.8-SNAPSHOT/webapp-1.0.0.8-20150326.143644-1.war
```

## Jenkins job: shell execute playbook
```bash
sudo su - ansible -c 'ansible-playbook -vvvv /home/ansible/workspace/webapp.yml'
```
