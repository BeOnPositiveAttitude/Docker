Смотреть детальную информацию о контейнере:

```shell
docker container inspect custom-webapp
```

Смотреть сколько ресурсов потребляют контейнеры:

```shell
$ docker container stats
CONTAINER ID   NAME            CPU %     MEM USAGE / LIMIT     MEM %     NET I/O      BLOCK I/O   PIDS
56309afe0732   custom-webapp   0.00%     1.609MiB / 3.824GiB   0.04%     1.3kB / 0B   0B / 0B     2
```

Смотреть список процессов в контейнере:

```shell
docker container top custom-webapp
```

Смотреть системные события docker:

```shell
docker system events --since 60m
```