# Artifactory

- Installation (centos6)

```bash
cd /tmp
wget https://bintray.com/artifact/download/jfrog/artifactory-rpms/artifactory-3.6.0.rpm
yum localinstall artifactory-3.6.0.rpm
```

- Start

```bash
chkconfig artifactory on
service artifactory start
```
