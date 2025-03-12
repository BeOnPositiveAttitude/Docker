Смотреть список volumes на хосте:

```shell
$ docker volume ls
```

Смотреть информацию по volume:

```shell
$ docker volume inspect data_volume
```

Удалить определенный volume:

```shell
$ docker volume rm data_volume
```

Удалить неиспользуемые volumes:

```shell
$ docker volume prune
```

По умолчанию volume монтируется в контейнер в режиме read-write. Можно переопределить при запуске контейнера:

```shell
$ docker container run --mount source=data_vol1,destination=/var/www/html/index.html,readonly httpd
```

### Volume Mount Demo

Создадим тестовый volume:

```
$ docker volume create testvol
testvol
```

Создадим тестовый контейнер:

```shell
$ docker container run -itd --rm --name=test -v testvol:/aidar redhat/ubi9
8c16d7078c6b46eb8f0067386dab5086afefff3771dbd0260f0c5dfbb89f1733
```

Посмотрим куда смонтировался volume:

```
$ docker exec -it test /bin/bash
[root@8c16d7078c6b /]# df -h
Filesystem                  Size  Used Avail Use% Mounted on
overlay                      48G   44G  1.2G  98% /
tmpfs                        64M     0   64M   0% /dev
shm                          64M     0   64M   0% /dev/shm
/dev/mapper/srv01--vg-root   48G   44G  1.2G  98% /aidar
tmpfs                       2.0G     0  2.0G   0% /proc/asound
tmpfs                       2.0G     0  2.0G   0% /proc/acpi
tmpfs                       2.0G     0  2.0G   0% /sys/firmware

### Создадим пару тестовых файлов в точке монтирования
[root@8c16d7078c6b /]# cd /aidar/
[root@8c16d7078c6b aidar]# echo "hello all" > abc
[root@8c16d7078c6b aidar]# echo "hello all again" > def
[root@8c16d7078c6b aidar]# ls -l
total 8
-rw-r--r-- 1 root root 10 Mar 12 06:15 abc
-rw-r--r-- 1 root root 16 Mar 12 06:16 def
```

Остановим контейнер (он удалится автоматически):

```shell
$ docker container stop test
test

$ docker container ls -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

Volume при этом никуда не делся:

```shell
$ docker volume ls
DRIVER    VOLUME NAME
local     testvol

$ docker volume inspect testvol
[
    {
        "CreatedAt": "2025-03-12T10:12:07+04:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/testvol/_data",
        "Name": "testvol",
        "Options": null,
        "Scope": "local"
    }
]
```

Посмотрим на содержимое каталога:

```shell
$ sudo ls -l /var/lib/docker/volumes/testvol/_data
[sudo] password for ayakhin:
total 8
-rw-r--r-- 1 root root 10 Mar 12 10:15 abc
-rw-r--r-- 1 root root 16 Mar 12 10:16 def

$ sudo cat /var/lib/docker/volumes/testvol/_data/abc
hello all

$ sudo cat /var/lib/docker/volumes/testvol/_data/def
hello all again
```

Создадим новый контейнер и смонтируем в него volume `testvol`:

```shell
$ docker container run -itd --rm --name=testagain --mount source=testvol,destination=/aidar redhat/ubi9
0e6c63414f74cc5df8cfee22f87904bd754e0ed83f13d1ed4186d23ead904741
```

Подключимся и проверим содержимое:

```
$ docker container exec -it testagain /bin/bash
[root@0e6c63414f74 /]# df -h
Filesystem                  Size  Used Avail Use% Mounted on
overlay                      48G   44G  1.2G  98% /
tmpfs                        64M     0   64M   0% /dev
shm                          64M     0   64M   0% /dev/shm
/dev/mapper/srv01--vg-root   48G   44G  1.2G  98% /aidar
tmpfs                       2.0G     0  2.0G   0% /proc/asound
tmpfs                       2.0G     0  2.0G   0% /proc/acpi
tmpfs                       2.0G     0  2.0G   0% /sys/firmware
[root@0e6c63414f74 /]# cd /aidar
[root@0e6c63414f74 aidar]# ls -l
total 8
-rw-r--r-- 1 root root 10 Mar 12 06:15 abc
-rw-r--r-- 1 root root 16 Mar 12 06:16 def
[root@0e6c63414f74 aidar]# cat abc
hello all
[root@0e6c63414f74 aidar]# cat def
hello all again
```

По умолчанию volume монтируется в контейнер в режиме read-write. Проверим:

```shell
$ docker container run -itd --rm --name=container-volrw --mount source=data_vol1,destination=/aidar redhat/ubi9
86c7d0fa73a1b326295dac72e6912efc32564d455ceb30106e468503ffbdacec
```

```json
$ docker container inspect container-volrw | jq .[].Mounts
[
  {
    "Type": "volume",
    "Name": "data_vol1",
    "Source": "/var/lib/docker/volumes/data_vol1/_data",
    "Destination": "/aidar",
    "Driver": "local",
    "Mode": "z",
    "RW": true,
    "Propagation": ""
  }
]
```

Запустим другой контейнер и смонтируем volume в режиме readonly:

```shell
$ docker container run -itd --rm --name=container-volro --mount source=data_vol1,destination=/aidar,readonly redhat/ubi9
7d7b168df98b81e7ef853ab3987fc4efa92ffceff9fadf35f65209ca2dd7a6d4
```

```json
$ docker container inspect container-volro | jq .[].Mounts
[
  {
    "Type": "volume",
    "Name": "data_vol1",
    "Source": "/var/lib/docker/volumes/data_vol1/_data",
    "Destination": "/aidar",
    "Driver": "local",
    "Mode": "z",
    "RW": false,
    "Propagation": ""
  }
]
```

### Bind Mount Demo

```
$ docker container run -itd --rm --name=test --mount type=bind,source=/home/ayakhin/data,destination=/aidar redhat/ubi9
11bd811bffc433cf5ff633833f18d550e5dd38a1b5e4726d66e612e1c626af4a

$ docker container exec -it test /bin/bash
[root@11bd811bffc4 /]# df -h
Filesystem                  Size  Used Avail Use% Mounted on
overlay                      48G   44G  1.2G  98% /
tmpfs                        64M     0   64M   0% /dev
shm                          64M     0   64M   0% /dev/shm
/dev/mapper/srv01--vg-root   48G   44G  1.2G  98% /aidar
tmpfs                       2.0G     0  2.0G   0% /proc/asound
tmpfs                       2.0G     0  2.0G   0% /proc/acpi
tmpfs                       2.0G     0  2.0G   0% /sys/firmware
```

Which among the below is a correct command to start a `webapp` container with the volume `vol3`, mounted to the destination directory `/opt` in readonly mode? Далее перечислены верные ответы:

```shell
$ docker run -d --name=webapp -v vol3:/opt:ro httpd
$ docker run -d --name=webapp --mount source=vol3,target=/opt,readonly httpd
$ docker run -d --name=webapp --mount source=vol3,target=/opt,ro httpd
$ docker run -d --name=webapp --volume vol3:/opt:ro httpd
```