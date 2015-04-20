# Docker
## Requirements
- Linux Kernel >= 2.6.32-431
- epel repo
```bash
yum install http://mirror.pnl.gov/epel/6/i386/epel-release-6-8.noarch.rpm
```

## Installation (centos6)
```bash
yum install docker-io
```

## Configuration
- File System
```bash
mkdir -p /data/docker
mkdir -p /data/tmp
lvcreate -n lv_docker_data vg_app -L 20G
lvcreate -n lv_docker_metadata vg_app -L 2G
dd if=/dev/zero of=/dev/vg_app/lv_docker_metadata bs=1M count=2000
```

- /etc/sysconfig/docker
```
other_args="-g /data/docker"
DOCKER_CERT_PATH=/etc/docker
DOCKER_TMPDIR=/data/tmp
```

- /etc/sysconfig/docker-storage
```
DOCKER_STORAGE_OPTIONS="--storage-opt dm.datadev=/dev/vg_app/lv_docker_data --storage-opt dm.metadatadev=/dev/vg_app/lv_docker_metadata"
```

## User profile
- ~/.profile
```
# Docker
alias docker-ips='docker ps | tail -n +2 | while read -a a; do name=${a[$((${#a[@]}-1))]}; echo -ne "$name\t"; docker inspect $name | grep IPAddress | cut -d \" -f 4; done'

function docker-bash {
  docker exec -it $1 /bin/bash
}
```

## Commands
*Start*
```bash
service docker start
```
```
Starting cgconfig service:                                 [  OK  ]
Starting docker:                                           [  OK  ]
```

*Pull image*
```bash
docker pull centos:centos6
```

*Images list*
```bash
docker images
```
```
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
centos              centos6             f6808a3e4d9e        6 weeks ago         215.7 MB
```

*Images run*
```bash
docker run -d -it centos:centos6 bash
```
```
15c424f5c15c9e4f9af55c090c70804b7cd03f043fd3aca00ac63a17ca68c549
```

*Container list*
```bash
docker ps
```
```
CONTAINER ID        IMAGE                   COMMAND             CREATED             STATUS              PORTS                   NAMES
15c424f5c15c        centos:centos6          "bash"              41 seconds ago      Up 35 seconds                               jolly_kowalevski
```

*Container inspect*
```bash
docker inspect 15c424f5c15c
```

*Container execute*
```bash
docker exec -it 15c424f5c15c bash
```
```
[root@15c424f5c15c /]#
```
