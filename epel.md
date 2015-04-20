# EPEL
- Add EPEL repo
```bash
wget http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
yum install epel-release-6-8.noarch.rpm
sudo sed -i "s/mirrorlist=https/mirrorlist=http/" /etc/yum.repos.d/epel.repo
```
