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

##### DIN # DOCKER COMPOSE #####

curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
/usr/local/bin/docker-compose --help

Fast, isolated development environments using Docker.
Usage:
  docker-compose [options] [COMMAND] [ARGS...]
  docker-compose -h|--help
Options:
  --verbose                 Show more output
  --version                 Print version and exit
  -f, --file FILE           Specify an alternate compose file (default: docker-compose.yml)
  -p, --project-name NAME   Specify an alternate project name (default: directory name)
Commands:
  build     Build or rebuild services
  help      Get help on a command
  kill      Kill containers
  logs      View output from containers
  port      Print the public port for a port binding
  ps        List containers
  pull      Pulls service images
  rm        Remove stopped containers
  run       Run a one-off command
  scale     Set number of containers for a service
  start     Start services
  stop      Stop services
  restart   Restart services
  up        Create and start containers

##### DEX # USER PROFILE ##### 

cat .profile
# Docker
alias docker-ips='docker ps | tail -n +2 | while read -a a; do name=${a[$((${#a[@]}-1))]}; echo -ne "$name\t"; docker inspect $name | grep IPAddress | cut -d \" -f 4; done'

function docker-bash {
  docker exec -it $1 /bin/bash
}

##### DEX # DOCKER START ##### 
 
service docker start
# Starting cgconfig service:                                 [  OK  ]
# Starting docker:                                           [  OK  ]
docker ps
# CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

##### HOST DOCKER #####
docker run --name some-postgres -e POSTGRES_PASSWORD=mysecretpassword -d postgres
docker run -it --link some-postgres:postgres --rm postgres sh -c 'exec psql -h "$POSTGRES_PORT_5432_TCP_ADDR" -p "$POSTGRES_PORT_5432_TCP_PORT" -U postgres'
# Password for user postgres:
# psql (9.4.1)
# Type "help" for help.
# postgres=# help
# You are using psql, the command-line interface to PostgreSQL.
# Type:  \copyright for distribution terms
#        \h for help with SQL commands
#        \? for help with psql commands
#        \g or terminate with semicolon to execute query
#        \q to quit
# postgres=# \q

docker images
# REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
# postgres            latest              2b141c932837        3 days ago          214.2 MB

docker ps
# CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
# ef26a4b46636        postgres:latest     "/docker-entrypoint.   5 minutes ago       Up 5 minutes        5432/tcp            some-postgres

docker run -it --link some-postgres:postgres --rm postgres sh -c 'exec bash'                                                      root@23d69c77fb05:/# uname -a
##### @6301851f1872 DOCKER POSTGRESQL #####
Linux 23d69c77fb05 2.6.32-504.12.2.el6.x86_64 #1 SMP Wed Mar 11 22:03:14 UTC 2015 x86_64 GNU/Linux
root@6301851f1872:/# lsb_release -a
No LSB modules are available.
Distributor ID: Debian
Description:    Debian GNU/Linux 7.8 (wheezy)
Release:        7.8
Codename:       wheezy
root@6301851f1872:/# dpkg -l | grep post
ii  pgdg-keyring                    2014.1                all          keyring for apt.postgresql.org
ii  postgresql-9.4                  9.4.1-1.pgdg70+1      amd64        object-relational SQL database, version 9.4 server
ii  postgresql-client-9.4           9.4.1-1.pgdg70+1      amd64        front-end programs for PostgreSQL 9.4
ii  postgresql-client-common        166.pgdg70+1          all          manager for multiple PostgreSQL client versions
ii  postgresql-common               166.pgdg70+1          all          PostgreSQL database-cluster manager
ii  postgresql-contrib-9.4          9.4.1-1.pgdg70+1      amd64        additional facilities for PostgreSQL
root@2ba4b5d44e5b:/# echo $POSTGRES_PORT_5432_TCP_ADDR
172.17.0.2
root@2ba4b5d44e5b:/# echo $POSTGRES_PORT_5432_TCP_PORT
5432
root@23d69c77fb05:/# lsblk
NAME                                                                                             MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
loop0                                                                                              7:0    0   100G  0 loop
+-docker-253:7-140603-pool (dm-11)                                                               253:11   0   100G  0 dm
  +-docker-253:7-140603-ef26a4b466360c73470ffd339a8e2a6496a90b50e89c83bb21c1b810c906f224 (dm-12) 253:12   0    10G  0 dm
  +-docker-253:7-140603-23d69c77fb0592b30e29ec7bce8c538f0ceb5d5d1449789dfdbeb1a786d4bda5 (dm-13) 253:13   0    10G  0 dm   /
loop1                                                                                              7:1    0     2G  0 loop
+-docker-253:7-140603-pool (dm-11)                                                               253:11   0   100G  0 dm
  +-docker-253:7-140603-ef26a4b466360c73470ffd339a8e2a6496a90b50e89c83bb21c1b810c906f224 (dm-12) 253:12   0    10G  0 dm
  +-docker-253:7-140603-23d69c77fb0592b30e29ec7bce8c538f0ceb5d5d1449789dfdbeb1a786d4bda5 (dm-13) 253:13   0    10G  0 dm   /
sda                                                                                                8:0    0   100G  0 disk
+-sda1                                                                                             8:1    0   256M  0 part
...
root@23d69c77fb05:/# exit
exit

##### HOST DOCKER #####
docker info
# Containers: 1
# Images: 22
# Storage Driver: devicemapper
#  Pool Name: docker-253:7-140603-pool
#  Pool Blocksize: 65.54 kB
#  Data file: /var/lib/docker/devicemapper/devicemapper/data
#  Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
#  Data Space Used: 594 MB
#  Data Space Total: 107.4 GB
#  Metadata Space Used: 1.483 MB
#  Metadata Space Total: 2.147 GB
#  Library Version: 1.02.89-RHEL6 (2014-09-01)
# Execution Driver: native-0.2
# Kernel Version: 2.6.32-504.12.2.el6.x86_64
# Operating System: <unknown>
# CPUs: 2
# Total Memory: 3.743 GiB

ifconfig
# docker0   Link encap:Ethernet  HWaddr D6:05:5B:4E:AD:49
#           inet adr:172.17.42.1  Bcast:0.0.0.0  Masque:255.255.0.0
#           UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
#           RX packets:2 errors:0 dropped:0 overruns:0 frame:0
#           TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
#           collisions:0 lg file transmission:0
#           RX bytes:56 (56.0 b)  TX bytes:0 (0.0 b)
# ...
# veth4c2d30d Link encap:Ethernet  HWaddr D6:05:5B:4E:AD:49
#           UP BROADCAST RUNNING  MTU:1500  Metric:1
#           RX packets:24 errors:0 dropped:0 overruns:0 frame:0
#           TX packets:36 errors:0 dropped:0 overruns:0 carrier:0
#           collisions:0 lg file transmission:0
#           RX bytes:1988 (1.9 KiB)  TX bytes:2396 (2.3 KiB)

telnet 172.17.0.2 5432
# Trying 172.17.0.2...
# Connected to 172.17.0.2.
# Escape character is '^]'.
# HELO
# Connection closed by foreign host.

route -n
# Table de routage IP du noyau
# Destination     Passerelle      Genmask         Indic Metric Ref    Use Iface
# 192.168.29.0    0.0.0.0         255.255.255.0   U     0      0        0 eth0
# 172.17.0.0      0.0.0.0         255.255.0.0     U     0      0        0 docker0
# 0.0.0.0         192.168.29.250  0.0.0.0         UG    0      0        0 eth0

arp -an
# ? (192.168.29.250) at 00:00:5e:00:01:0e [ether] on eth0
# ? (172.17.0.2) at 02:42:ac:11:00:02 [ether] on docker0

###### DOCKER API
echo -e "GET /images/json HTTP/1.0\r\n" | nc -U /var/run/docker.sock
HTTP/1.0 200 OK
Content-Type: application/json
Date: Tue, 07 Apr 2015 16:05:38 GMT

[{"Created":1428420957,"Id":"699c4816100760fbb578b3d00cac4f7ac10160991b82aedb8203851f75ad2676","ParentId":"9a9905376b14153d2383bf7874d75f228bc31f1acc6dbf2cc51b85a9dc73ca73","RepoTags":["xxx/yyy:1.0.0.8","xxx/yyy:1.0.0.8"],"Size":147470,"VirtualSize":553846735}
...
