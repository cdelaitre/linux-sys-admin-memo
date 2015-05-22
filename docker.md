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
- docker local tool :
```bash
cat /usr/local/bin/docker-bash
#!/bin/bash
CONTAINER=$1
docker exec -it ${CONTAINER} /bin/bash
```

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

## Commands
### Docker service
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
[admcdela@acdevjenkins:~]# docker images
REPOSITORY                      TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
cdelaitre/centos-postgres       91                  5b6c867d6f89        5 weeks ago         362.3 MB
cdelaitre/centos-tomcat         7                   53974c044e34        6 weeks ago         508.1 MB
cdelaitre/centos-tomcat         8                   2ca5a7fb2d10        6 weeks ago         477.5 MB
cdelaitre/centos-openjdk        7                   dfa56d8c1a14        6 weeks ago         480.8 MB
cdelaitre/centos-openjdk        8                   25ecddcd0ec7        6 weeks ago         451.3 MB
i3p_ci/redmine                  latest              a748da282061        6 weeks ago         1.481 GB
cdelaitre/centos                6                   2485d27f35a4        6 weeks ago         237.6 MB
cdelaitre/postgres93            latest              580d5a0ba752        6 weeks ago         860.2 MB
cdelaitre/postgres91            latest              dfabe6ecd462        6 weeks ago         858.2 MB
i3p_ci_app/baignade_web         4.2.1.8             699c48161007        6 weeks ago         553.8 MB
i3p_ci_app/baignade_web         4_2_1_8             699c48161007        6 weeks ago         553.8 MB
i3p_ci_app/baignadepg91         4_2_1_8             c7f2b0a07d4a        6 weeks ago         4.69 GB
i3p_ci_app/baignade_pg91        4.2.1.8             c7f2b0a07d4a        6 weeks ago         4.69 GB
i3p_ci/postgres91               latest              ff1400d8c379        6 weeks ago         855.8 MB
i3p_ci_app/baignadeweb4_2_1_8   latest              dc1cba572471        6 weeks ago         581.9 MB
i3p_ci/centos6                  latest              85710958c8d2        7 weeks ago         235.4 MB
i3p_ci/postgres93               latest              2b01272bc2c8        7 weeks ago         872 MB
i3p_ci_app/baignadeweb          4.2.1.8             9a9905376b14        7 weeks ago         553.7 MB
i3p_ci/tomcat7                  latest              0c19fbe71795        7 weeks ago         503.8 MB
i3p_ci/openjdk7                 latest              2506fb661831        7 weeks ago         476.5 MB
centos                          centos6             f6808a3e4d9e        11 weeks ago        215.7 MB
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
docker run -d -it -p 52010:80 -v /data/redmine:/var/lib/mysql i3p_ci/redmine bash

docker ps

CONTAINER ID        IMAGE                       COMMAND                CREATED             STATUS              PORTS                             NAMES
72d260f1ac7f        i3p_ci/redmine:latest       "bash"                 2 weeks ago         Up 2 weeks          0.0.0.0:52010->80/tcp             adoring_hopper    

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
72d260f1ac7f        i3p_ci/redmine:latest       "bash"                 2 weeks ago         Up 2 weeks          0.0.0.0:52010->80/tcp             adoring_hopper    

docker-bash 72d260f1ac7f
[root@72d260f1ac7f /]# service httpd stop
[root@72d260f1ac7f /]# service mysqld stop

docker stop 72d260f1ac7f
```
