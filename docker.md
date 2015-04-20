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

## Start
```bash
service docker start
```
