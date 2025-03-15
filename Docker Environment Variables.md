Чтобы не "хардкодить" некоторые параметры приложения, их выносят в переменные.

Запуск контейнера с указанием переменной окружения:

```shell
$ docker run -e APP_COLOR=blue simple-webapp-color
$ docker run --env APP_COLOR=blue simple-webapp-color
```

Посмотреть environment variables в уже запущенном контейнере можно командой:

```shell
$ docker inspect container_name
```

В Dockerfile переменные окружения можно указывать двумя способами:

```Dockerfile
ENV MYVAR myvalue
ENV MYVAR=myvalue
```