Выгрузить образ, уже загруженный на Docker-хост, в архив:

```shell
$ docker image save alpine:latest -o alpine.tar
```

Загрузить образ на Docker-хосте из архива:

```shell
$ docker image load -i alpine.tar
```

Сделать read-only template (образ) из бегущего контейнера:

```shell
$ docker export <container-name> > testcontainer.tar
```

Далее этот образ можно импортировать на любой системе:

```shell
$ docker image import testcontainer.tar newimage:latest
```