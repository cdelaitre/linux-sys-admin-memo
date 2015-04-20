# Docker
## Requirements
- Linux Kernel >= 2.6.32-431
- Add [EPEL repo](epel.md)

## Installation (centos6)
```bash
yum install docker-io
```

## Configure storage
**Assume**
- vg_data: data volume group
- lv_data: data logical volume mounted to /data

**Create docker storage**
```bash
mkdir -p /data/docker
mkdir -p /data/dockertmp
lvcreate -n lv_docker_data vg_data -L 20G
lvcreate -n lv_docker_metadata vg_data -L 2G
dd if=/dev/zero of=/dev/vg_data/lv_docker_metadata bs=1M count=2000
```

**/etc/sysconfig/docker**
```
other_args="-g /data/docker"
DOCKER_CERT_PATH=/etc/docker
DOCKER_TMPDIR=/data/dockertmp
```

**/etc/sysconfig/docker-storage**
```
DOCKER_STORAGE_OPTIONS="--storage-opt dm.datadev=/dev/vg_data/lv_docker_data --storage-opt dm.metadatadev=/dev/vg_data/lv_docker_metadata"
```

## User profile
**~/.profile**
```
# Docker
alias docker-ips='docker ps | tail -n +2 | while read -a a; do name=${a[$((${#a[@]}-1))]}; echo -ne "$name\t"; docker inspect $name | grep IPAddress | cut -d \" -f 4; done'

function docker-bash {
  docker exec -it $1 /bin/bash
}
```

## Commands
### Docker
**Start**
```bash
service docker start
```
```
Starting cgconfig service:                                 [  OK  ]
Starting docker:                                           [  OK  ]
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

**List**
```bash
docker images
```
```
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
centos              centos6             f6808a3e4d9e        6 weeks ago         215.7 MB
```

**Run**
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
