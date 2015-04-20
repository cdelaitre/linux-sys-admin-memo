# ANSIBLE
## installation
### pre-requirement
#### python-paramiko
- disable rpmforge extra repo
- if python-paramiko.noarch 0:1.7.6-1.el6.rfx installed => remove it
```bash
yum remove python-paramiko
```

### command
```bash
yum install ansible
---> Package ansible.noarch 0:1.8.4-1.el6 will be installed
---> Package python-paramiko.noarch 0:1.7.5-2.1.el6 will be installed
```
### test
```bash
ansible --version
 [WARNING]: The version of gmp you have installed has a known issue regarding
timing vulnerabilities when used with pycrypto. If possible, you should update
it (i.e. yum update gmp).

ansible 1.8.4
  configured module search path = None
```

## CLIENT (192.168.1.2): CONF #####
[root] useradd ansible
[root] passwd ansible
Dans /etc/sudoers
Defaults    !requiretty
ansible         ALL=(ALL)       NOPASSWD: ALL

##### HOST : CONF #####  
fichier /etc/sudoers
Defaults    !requiretty

fichier /etc/ansible/hosts
[appli:vars]
ansible_ssh_user=ansible
[appli]
192.168.1.2

useradd ansible
su - ansible
ssh-keygen -t rsa -C "ansible@ansiblemaster"
ssh-copy-id 192.168.1.2

##### VERIFY KEY #####
su - ansible
cat .ssh/authorized_keys
# ssh-rsa XXXXX ansible@ansiblemaster

##### ANSIBLE PING #####
ansible appli -m ping
[WARNING]: The version of gmp you have installed has a known issue regarding
timing vulnerabilities when used with pycrypto. If possible, you should update
it (i.e. yum update gmp).

192.168.1.2 | success >> {
    "changed": false,
    "ping": "pong"
}

# appli.yml
---
- name: Pull Artifactory war to tomcat7
  hosts: appli
  tasks:
  - name: clean war
    command: rm -f /produits/tomcat7/webapps/appli.war
  - name: download war
    get_url:
      use_proxy: no
      dest: /produits/tomcat7/webapps/appli.war
      url: http://192.168.1.1:8081/artifactory/libs-snapshot-local/1.0.0.8-SNAPSHOT/appli-1.0.0.8-20150326.143644-1.war

# Jenkins ANSIBLE: Execute shell
sudo su - ansible -c 'ansible-playbook -vvvv /home/ansible/workspace/appli.yml'