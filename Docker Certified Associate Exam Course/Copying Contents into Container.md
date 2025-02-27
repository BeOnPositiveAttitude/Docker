Скопировать файл с Docker-хоста в контейнер:

```shell
docker container cp /tmp/web.conf webapp:/etc/web.conf
```

Скопировать файл из контейнера на Docker-хост:

```shell
docker container cp webapp:/root/dockerhost /tmp/
```