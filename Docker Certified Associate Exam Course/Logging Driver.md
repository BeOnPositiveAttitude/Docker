Logging Driver определяет где и как должны сохраняться логи контейнеров.

Logging Driver по умолчанию - `json-file`.

```shell
$ docker system info | grep -i logging
 Logging Driver: json-file
```

Где сохраняются логи контейнеров?

```shell
$ docker container run -d --rm --name=apache httpd
0d5a48e44b477c40012d1f58a81ba063a70f77aae6ae67fedb7f22d21ee7ee62

$ docker container ls -l
CONTAINER ID   IMAGE     COMMAND              CREATED         STATUS         PORTS     NAMES
0d5a48e44b47   httpd     "httpd-foreground"   7 seconds ago   Up 6 seconds   80/tcp    apache

$ docker container logs apache
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.2. Set the 'ServerName' directive globally to suppress this message
AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.2. Set the 'ServerName' directive globally to suppress this message
[Fri Feb 28 12:12:36.633563 2025] [mpm_event:notice] [pid 1:tid 1] AH00489: Apache/2.4.63 (Unix) configured -- resuming normal operations
[Fri Feb 28 12:12:36.633918 2025] [core:notice] [pid 1:tid 1] AH00094: Command line: 'httpd -D FOREGROUND'

$ sudo ls -l /var/lib/docker/containers
total 20
drwx--x--- 4 root root 4096 Feb 28 16:12 0d5a48e44b477c40012d1f58a81ba063a70f77aae6ae67fedb7f22d21ee7ee62
drwx--x--- 4 root root 4096 Feb 28 16:09 541687d78d18203e8ee786c550d93d56a2747215a0c78a7809db94cad6e071d2
drwx--x--- 4 root root 4096 Feb 28 16:09 737829cca4ec84871bd65f88eb291ef9cd66960be468815d06645f2a9d600ed3
drwx--x--- 4 root root 4096 Feb 28 16:09 a5dbc4604cb95b4a38c8e70e68a1b920dc6285b18b951677af96ab01c2131f32
drwx--x--- 4 root root 4096 Feb 28 15:34 f719bccf395ce84e08fe485ff0d990882016719a19f1dbb73230920bcdc3aee1

# Смотрим содержимое каталога нашего контейнера, тут есть файл с именем "id контейнера + json.log"
$ sudo ls -l /var/lib/docker/containers/0d5a48e44b477c40012d1f58a81ba063a70f77aae6ae67fedb7f22d21ee7ee62
total 36
-rw-r----- 1 root root  865 Feb 28 16:12 0d5a48e44b477c40012d1f58a81ba063a70f77aae6ae67fedb7f22d21ee7ee62-json.log
drwx------ 2 root root 4096 Feb 28 16:12 checkpoints
-rw------- 1 root root 2877 Feb 28 16:12 config.v2.json
-rw------- 1 root root 1460 Feb 28 16:12 hostconfig.json
-rw-r--r-- 1 root root   13 Feb 28 16:12 hostname
-rw-r--r-- 1 root root  174 Feb 28 16:12 hosts
drwx--x--- 2 root root 4096 Feb 28 16:12 mounts
-rw-r--r-- 1 root root  271 Feb 28 16:12 resolv.conf
-rw-r--r-- 1 root root   71 Feb 28 16:12 resolv.conf.hash

$ sudo cat /var/lib/docker/containers/0d5a48e44b477c40012d1f58a81ba063a70f77aae6ae67fedb7f22d21ee7ee62/0d5a48e44b477c40012d1f58a81ba063a70f77aae6ae67fedb7f22d21ee7ee62-json.log

{"log":"AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.2. Set the 'ServerName' directive globally to suppress this message\n","stream":"stderr","time":"2025-02-28T12:12:36.626431532Z"}
{"log":"AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 172.17.0.2. Set the 'ServerName' directive globally to suppress this message\n","stream":"stderr","time":"2025-02-28T12:12:36.630972877Z"}
{"log":"[Fri Feb 28 12:12:36.633563 2025] [mpm_event:notice] [pid 1:tid 1] AH00489: Apache/2.4.63 (Unix) configured -- resuming normal operations\n","stream":"stderr","time":"2025-02-28T12:12:36.633866797Z"}
{"log":"[Fri Feb 28 12:12:36.633918 2025] [core:notice] [pid 1:tid 1] AH00094: Command line: 'httpd -D FOREGROUND'\n","stream":"stderr","time":"2025-02-28T12:12:36.633958298Z"}
```

Существуют и другие Logging Drivers.

```shell
docker system info
...
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local splunk syslog
...
```

Можно даже задать опцию `none`, чтобы логи контейнеров не сохранялись.

Задать нужный Logging Driver можно в конфигурационном файл `/etc/docker/daemon.json`.

```json
{
    "debug": true,
    "hosts": ["tcp://192.168.56.104:2376"],
    "tls": true,
    "tlscert": "/var/docker/server.pem",
    "tlskey": "/var/docker/serverkey.pem",
    "log-driver": "awslogs"
}
```

Некоторые Logging Driver требуют указания дополнительных опций.

```json
{
    "debug": true,
    "hosts": ["tcp://192.168.56.104:2376"],
    "tls": true,
    "tlscert": "/var/docker/server.pem",
    "tlskey": "/var/docker/serverkey.pem",
    "log-driver": "awslogs",
    "log-opt": {
        "awslogs-region": "us-east-1"
    }
}
```

А для авторизации в AWS аккаунте необходимо задать environment variables:

```shell
export AWS_ACCESS_KEY_ID=<>
export AWS_SECRET_ACCESS_KEY=<>
export AWS_SESSION_TOKEN=<>
```

Можно переопределить Logging Driver при запуске контейнера:

```shell
docker container run -d --name=apache --log-driver=journald httpd
```

Посмотреть какой Logging Driver использует контейнер:

```shell
docker container inspect apache
...
"HostConfig": {
    "Binds": null,
    "ContainerIDFile": "",
    "LogConfig": {
        "Type": "json-file",
        "Config": {}
    },
...
```

Или так:

```shell
docker container inspect -f '{{ .HostConfig.LogConfig.Type }}' apache
json-file
```