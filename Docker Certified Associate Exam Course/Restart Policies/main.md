Задать политику перезапуска контейнера:

```shell
docker container run --restart=<policy_name> ubuntu
```

<img src="image.png" width="900" height="350"><br>

Политики могут быть следующие:
- `no` - политика по умолчанию, не перезапускать контейнер
- `on-failure` - перезапускать в случае падения (если exit code не равен 0)
- `always` - перезапускать в любом случае, независимо от exit code; однако если контейнер был остановлен вручную, то перезапустится он только в случае перезапуска Docker Daemon
- `unless-stopped` - перезапускать в любом случае, независимо от exit code, но не перезапускать контейнер, если он был остановлен вручную (и даже если будет перезапущен Docker Daemon)

Важно помнить, что данные политики применимы только если контейнер успешно запустился первый раз (находился в запущенном состоянии хотя бы 10 секунд).

Когда Docker Daemon по какой-либо причине останавливает свою работу, то по умолчанию падают все контейнеры. Однако можно настроить Docker Daemon таким образом, что контейнеры продолжали свою работу в случае падения демона. Делается это в файле `/etc/docker/daemon.json` с помощью опции `live-restore`.

```json
{
    "debug": true,
    "hosts": ["tcp://192.168.56.104:2376"],
    "live-restore": true
}
```

### Restart policy `no`

```shell
$ docker container run -itd --name=caseone --restart=no ubuntu
8cb3e684aaa990e25f355bc7cde807628de0bf9c699401857a0d356e56121b92

$ docker container ls -l
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
8cb3e684aaa9   ubuntu    "/bin/bash"   5 seconds ago   Up 4 seconds             caseone

$ docker container stop caseone
caseone

$ docker container ls -l
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                       PORTS     NAMES
8cb3e684aaa9   ubuntu    "/bin/bash"   47 seconds ago   Exited (137) 3 seconds ago             caseone
```

### Restart policy `on-failure`

```shell
$ docker container run -itd --name=casetwo --restart=on-failure ubuntu
1a6ffd83b937892106c53c621a0dac8eae9c244228d187043e7e9a777a273629

$ docker container ls -l
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
1a6ffd83b937   ubuntu    "/bin/bash"   2 minutes ago   Up 2 minutes             casetwo

# Смотрим PID процесса в контейнере:
$ docker container top casetwo
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                33040               33019               0                   10:05               pts/0               00:00:00            /bin/bash

# Принудительно завершаем его, чтобы сымитировать exit code не равный 0:
$ sudo kill -9 33040

# Контейнер автоматически запустился:
$ docker container ls -l
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
1a6ffd83b937   ubuntu    "/bin/bash"   4 minutes ago   Up 2 seconds             casetwo
```

### Restart policy `always`

```shell
$ docker container run -itd --name=casethree --restart=always ubuntu
9b1283007fd8cfe4ceaca6607f587f88b63c09c54b3164a761714aa946b4a7eb

$ docker container ls -l
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS          PORTS     NAMES
9b1283007fd8   ubuntu    "/bin/bash"   25 seconds ago   Up 24 seconds             casethree

$ docker container stop casethree
casethree

$ docker container ls -l
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                       PORTS     NAMES
9b1283007fd8   ubuntu    "/bin/bash"   45 seconds ago   Exited (137) 3 seconds ago             casethree

$ sudo systemctl restart docker.service

# После рестарта демона контейнер поднялся
$ docker container ls -l
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
9b1283007fd8   ubuntu    "/bin/bash"   2 minutes ago   Up 3 seconds             casethree
```

### Restart policy `unless-stopped`

```shell
$ docker container run -itd --name=casefour --restart=unless-stopped ubuntu
9fc36dc99f1cef034865bf7cb7b7244e0b7b825be7d4939920e41103a153e5f5

$ docker container ls -l
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
9fc36dc99f1c   ubuntu    "/bin/bash"   5 seconds ago   Up 4 seconds             casefour

$ docker container stop casefour
casefour

$ docker container ls -l
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                       PORTS     NAMES
9fc36dc99f1c   ubuntu    "/bin/bash"   52 seconds ago   Exited (137) 3 seconds ago             casefour

$ sudo systemctl restart docker.service

# В данном случае контейнер не поднялся даже после перезапуска демона:
$ docker container ls -l
CONTAINER ID   IMAGE     COMMAND       CREATED              STATUS                        PORTS     NAMES
9fc36dc99f1c   ubuntu    "/bin/bash"   About a minute ago   Exited (137) 56 seconds ago             casefour
```