Репозиторий проекта: https://github.com/dockersamples/example-voting-app

Собираем образы (в соответствующих каталогах):

```shell
$ docker image build -t voting-app .
$ docker image build -t worker-app .
$ docker image build -t result-app .
```

Поднимаем контейнеры:

```shell
$ docker container run -d --name=redis redis:alpine
$ docker container run -d -p 5000:80 --link redis:redis voting-app
$ docker container run -d --name=db -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres postgres:15-alpine
$ docker container run -d --link redis:redis --link db:db worker-app
$ docker container run -d -p 5001:80 --link db:db result-app
```