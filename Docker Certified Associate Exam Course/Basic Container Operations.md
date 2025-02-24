Синтаксис docker-команды:

```shell
docker   <docker-object>   <sub-command>   [options]   <Arguments/Commands>
```

Например:

| Новый синтаксис | Старый синтаксис |
| ----------- | ----------- |
| `docker container ls` | `docker ps` |
| `docker images ls` | `docker images` |
| `docker network ls` | Аналогичный |
| `docker volume ls` | Аналогичный |
| `docker container run -it ubuntu` | `docker run -it ubuntu` |
| `docker image build .` | `docker build .` |
| `docker container attach ubuntu` | `docker attach ubuntu` |
| `docker container kill ubuntu` | `docker kill ubuntu` или `docker stop ubuntu` |


Здесь `container`, `image`, `network`, `volume` являются docker-объектами. Соответственно команды группируются в соответствии с объектами.

Команда `docker container create httpd` только создает контейнер, но не запускает его.

```shell
$ docker container create httpd
Unable to find image 'httpd:latest' locally
latest: Pulling from library/httpd
c29f5b76f736: Pull complete
830a84f99cc8: Pull complete
4f4fb700ef54: Pull complete
a1a1b409f475: Pull complete
35b1ecb71608: Pull complete
80350326cd93: Pull complete
Digest: sha256:3195404327ecd95b2fa0a5d4eac1f2206bb12996fb2561393f91254759e422b9
Status: Downloaded newer image for httpd:latest
2f418ef8d15a8bd3c0517f1db225c724ff185cf7d98abdca3fe86c51b3a3459d   # id контейнера
```

В каталоге `/var/lib/docker` по умолчанию создаются подкаталоги для всех docker-объектов, в том числе и для контейнеров. Там мы можем найти и наш созданный контейнер.

```shell
$ sudo ls -l /var/lib/docker/containers
total 24
drwx--x--- 3 root root 4096 Feb 24 22:35 2f418ef8d15a8bd3c0517f1db225c724ff185cf7d98abdca3fe86c51b3a3459d
...
```

Внутри него можно увидеть все файлы контейнера:

```shel
$ sudo ls -l /var/lib/docker/containers/2f418ef8d15a8bd3c0517f1db225c724ff185cf7d98abdca3fe86c51b3a3459d
total 12
drwx------ 2 root root 4096 Feb 24 22:35 checkpoints
-rw------- 1 root root 2120 Feb 24 22:35 config.v2.json
-rw------- 1 root root 1462 Feb 24 22:35 hostconfig.json
```

С помощью команды `docker container ls -a` можно посмотреть список всех контейнеров, запущенных и остановленных.

Смотреть какой контейнер был создан последним (`l = latest`):

```shell
$ docker container ls -l
CONTAINER ID   IMAGE     COMMAND              CREATED         STATUS    PORTS     NAMES
2f418ef8d15a   httpd     "httpd-foreground"   8 minutes ago   Created             trusting_poincare
```

Смотреть только id контейнеров (`q = quiet`):

```shell
$ docker container ls -qa
2f418ef8d15a
```