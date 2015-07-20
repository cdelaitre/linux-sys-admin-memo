# Docker
## Requirements
- Linux Kernel >= 2.6.32-431
- Add [EPEL repo](epel.md)

- Prepare docker storage
  - Directory /data/docker
  - Volume group : vg_data
  - Logical volumes : lv_docker_data, lv_docker_metadata
```bash
mkdir -p /data/docker
lvcreate -n lv_docker_data vg_data -L 20G
lvcreate -n lv_docker_metadata vg_data -L 2G
```

- Create tool /usr/local/bin/docker-bash
  - touch /usr/local/bin/docker-bash
  - chmod +x /usr/local/bin/docker-bash
  - vi /usr/local/bin/docker-bash
```bash
#!/bin/bash
CONTAINER=$1
docker exec -it ${CONTAINER} /bin/bash
```

## Installation Docker 1.7.1 (centos6)
```bash
curl -O -sSL https://get.docker.com/rpm/1.7.1/centos-6/RPMS/x86_64/docker-engine-1.7.1-1.el6.x86_64.rpm
yum localinstall --nogpgcheck docker-engine-1.7.1-1.el6.x86_64.rpm
```

## Start on boot
```bash
chkconfig docker on
```

## User config
```bash
usermod -aG docker __username__
```

**/etc/sysconfig/docker**
```
other_args="-g /data/docker -H tcp://0.0.0.0:52099 -H unix:///var/run/docker.sock"
```

## Commands
### Docker service
**Start**
```bash
service docker start
```

**Stop**
```bash
service docker stop
```

### Images
**Pull**
```bash
docker pull centos:centos6
```

**Save**
docker save centos:centos6 > /tmp/dockerimage-centos_centos6.tar

**Load**
docker load < /tmp/dockerimage-centos_centos6.tar

**List**
```bash
docker images
REPOSITORY                 TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
cdelaitre/centos-tomcat    8                   92d7bd94c08b        4 days ago          438.8 MB
cdelaitre/centos-tomcat    7                   c5f381c14470        4 days ago          465 MB
cdelaitre/centos-openjdk   8                   1f220192197d        4 days ago          413 MB
cdelaitre/centos-openjdk   7                   980e0ab4c7da        4 days ago          438.2 MB
cdelaitre/centos           6                   7870c50b5998        4 days ago          223.5 MB
centos                     centos6             a005304e4e74        4 weeks ago         203.1 MB
i3p_ci/redmine             latest              a748da282061        3 months ago        1.345 GB
adeo/testlink              latest              36781cbaa07d        10 months ago       687.6 MB
```

**Run bash on centos6**
```bash
docker run -d -it centos:centos6 bash
```
```
15c424f5c15c9e4f9af55c090c70804b7cd03f043fd3aca00ac63a17ca68c549
```

**Delete**
```bash
docker rmi -f centos:centos6
```

### Containers
**List**
```bash
docker ps
```
```
CONTAINER ID        IMAGE                   COMMAND             CREATED             STATUS              PORTS                   NAMES
15c424f5c15c        centos:centos6          "bash"              41 seconds ago      Up 35 seconds                               jolly_kowalevski
```

**Inspect**
```bash
docker inspect 15c424f5c15c
```
```
[{
    "AppArmorProfile": "",
    "Args": [],
    "Config": {
...
 "StartedAt": "2015-04-20T09:13:12.981147554Z"
    },
    "Volumes": {},
    "VolumesRW": {}
}
]
```

**Execute**
```bash
docker exec -it 15c424f5c15c bash
```
```
[root@15c424f5c15c /]#
```

**Stop**
```bash
docker stop 15c424f5c15c
```

### DEX Redmine

**Redmine Start**
```bash
docker run -d -it --name redmine -p 80:80 -v /data/redminemysql:/var/lib/mysql -v /data/redmine:/var/lib/redmine i3p_ci/redmine bash

docker ps

CONTAINER ID        IMAGE                       COMMAND                CREATED             STATUS              PORTS                             NAMES
72d260f1ac7f        i3p_ci/redmine:latest       "bash"                 2 weeks ago         Up 2 weeks          0.0.0.0:80->80/tcp             redmine    

docker-bash 72d260f1ac7f
[root@72d260f1ac7f /]# service mysqld start
mysqld started
[root@72d260f1ac7f /]# service httpd start
httpd started
```

**Redmine Stop**
```bash
docker ps

CONTAINER ID        IMAGE                       COMMAND                CREATED             STATUS              PORTS                             NAMES
72d260f1ac7f        i3p_ci/redmine:latest       "bash"                 2 weeks ago         Up 2 weeks          0.0.0.0:80->80/tcp             redmine    

docker-bash 72d260f1ac7f
[root@72d260f1ac7f /]# service httpd stop
[root@72d260f1ac7f /]# service mysqld stop

docker stop 72d260f1ac7f
```
