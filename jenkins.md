# Jenkins requirements
## OS Centos 6
## Java 1.7
- Installation
```bash
yum install java-1.7.0-openjdk-devel
```
## Ant 1.9.4
- Installation
```bash
TARGET=/produits/
mkdir -p  $TARGET
cd /tmp
wget http://mirrors.ircam.fr/pub/apache/ant/binaries/apache-ant-1.9.4-bin.zip
unzip apache-ant-1.9.4-bin.zip
mv apache-ant-1.9.4 $TARGET
cd $TARGET
ln -s apache-ant-1.9.4 ant
```
- Configuration
 - ant path: create **/etc/profile.d/ant.sh**
```
pathmunge /produits/ant/bin
```
## Maven 3.2.5
- Installation
```bash
TARGET=/produits/
mkdir -p  $TARGET
cd /tmp
wget http://mirrors.ircam.fr/pub/apache/maven/maven-3/3.2.5/binaries/apache-maven-3.2.5-bin.zip
unzip apache-maven-3.2.5-bin.zip
mv apache-maven-3.2.5 $TARGET
cd $TARGET
ln -s apache-maven-3.2.5 maven
```
- Configuration
 - maven path: create **/etc/profile.d/mvn.sh**
 ```
 pathmunge /produits/maven/bin
 ```
 - set proxy: modify **/produits/maven/conf/settings.xml**
 ```
<proxy>
  <id>proxy-corp</id>
  <active>true</active>
  <protocol>http</protocol>
  <host>A.B.C.D</host>
  <port>80</port>
  <nonProxyHosts>192.*|10.*</nonProxyHosts>
</proxy>
```

## Jenkins repo
- Add jenkins repo: create **/etc/yum.repos.d/jenkins.repo**
```
[jenkins]
name=Jenkins
baseurl=http://pkg.jenkins-ci.org/redhat
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-jenkins-ci
```
- Import key
```bash
rpm --import http://pkg.jenkins-ci.org/redhat/jenkins-ci.org.key
```

# Jenkins installation
```bash
yum install jenkins
```

# Start
```bash
chkconfig jenkins on
service jenkins start
```

# Update
```bash
yum update jenkins
```

# Optional
- Add firewall nat redirect 80 to 8080: modify **/etc/sysconfig/iptables**
```
*filter
...
# allow Jenkins
-A INPUT -m multiport -m comment --comment "Open port for Jenkins" -p tcp --dport 80 -j ACCEPT
-A INPUT -m multiport -m comment --comment "Open port for Jenkins" -p tcp --dport 8080 -j ACCEPT
...
COMMIT
*nat
:PREROUTING ACCEPT [1:48]
:POSTROUTING ACCEPT [13:1103]
:OUTPUT ACCEPT [13:1103]
-A PREROUTING -i eth0 -p tcp -m tcp --dport 80 -j REDIRECT --to-ports 8080
COMMIT
```
