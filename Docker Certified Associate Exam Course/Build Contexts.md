Вспомним пример из предыдущей лекции:

```Dockerfile
FROM ubuntu

RUN apt-get update && apt-get -y install python
RUN pip install flask flask-mysql
COPY . /opt/source-code       # здесь мы копируем файлы с Docker-хоста в образ
ENTRYPOINT FLASK_APP=/opt/source-code/app.py flask run
```

Как Docker Daemon понимает, где находятся файлы с исходным кодом нашего приложения?

Build Context - это путь, по которому находится Dockerfile и другие необходимые для сборки приложения файлы. Именно сюда смотрит Docker Daemon при сборке образа.

```shell
$ docker build .   # текущая директория
$ docker build /opt/my-custom-app   # можно указать абсолютный путь
```

Представим ситуацию, когда Docker CLI и Docker Daemon установлены на разных хостах. Не забываем про использование переменной `DOCKER_HOST` для указания адреса и порта хоста, на котором установлен Docker Daemon.

При выполнении команды `docker build` файлы, лежащие по пути, указанному в build context, копируются на хост с Docker Daemon в каталог `/var/lib/docker/tmp/docker-builderxxxxx`. Важно понимать, что файлы копируются по этому пути даже в случае, когда Docker CLI и Docker Daemon установлены на одном хосте.

В build context должны находиться **только** необходимые для сборки образа файлы. Иначе дополнительные мусорные файлы (например логи), находящиеся в build context, также будут переданы в образ, что увеличит его размер и время сборки.

Для того чтобы мусорные файлы не попали в целевой образ, можно создать файл `.dockerignore` и указать там названия файлов и папок для игнорирования.

Также при сборке можно указывать url репозитория, в котором находится Dockerfile.

```
$ docker build https://github.com/myaccount/myapp            # лучше добавлять .git в конце адреса репозитория
$ docker build https://github.com/myaccount/myapp#<branch>   # можно указать название ветки
$ docker build https://github.com/myaccount/myapp:<folder>   # можно указать название директории, в которой лежит Dockerfile
```

Если имя Dockerfile отличается от дефолтного, например `Dockerfile_prod`, тогда можно указать его через опцию `-f`:

```shell
$ docker image build -f Dockerfile_prod -t frenzy88/prodwebapp .
$ docker image build -t frenzy88/prodwebapp -f Dockerfile_prod ~/docker_tomcat/
```

Самое главное не забывать указывать build context!