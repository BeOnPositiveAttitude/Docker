Каждый слой в Dockerfile влияет на размер финального образа.

Каким образом кэш слоев, который создается при сборке образа, может стать невалидным? Если в каком-либо слое изменилась инструкция (например добавилась установка нового пакета) либо поменялось содержимое используемого при сборке файла (например `app.py`), тогда кэш станет невалидным, и Docker начнет заново пересобирать образ, начиная с этого слоя (и далее вниз по всем оставшимся).

Каким образом Docker понимает, что изменилось содержимое используемого при сборке файла (`app.py`)? При выполнении инструкции `COPY` и `ADD` Docker проверяет контрольные суммы файлов.

### Cache Busting

```Dockerfile
FROM ubuntu

RUN apt-get update
RUN apt-get install -y python python3-pip

RUN pip3 install flask flask-mysql

COPY app.py /opt/source-code

ENTRYPOINT flask run
```

Чем такой Dockerfile лучше предыдущего?

```Dockerfile
FROM ubuntu

RUN apt-get update && apt-get install -y \
    python python3-pip

RUN pip3 install flask flask-mysql

COPY app.py /opt/source-code

ENTRYPOINT flask run
```

Дело в том, что в первом варианте Dockerfile команды `apt-get update` и `apt-get install -y python python3-pip` разнесены в отдельные инструкции, а соответственно это будут разные слои образа. Если в первом случае мы захотим добавить установку нового пакета `apt-get install -y python python3-pip python-dev` и пересобрать образ, то процесс rebuild начнется сразу с установки пакетов, минуя этап обновления информации о пакетах в репозитории, что может привести к установке устаревшей версии пакета.

Во втором случае эти команды объединены в одну инструкцию и этой проблемы удастся избежать. Данная техника получила название Cache Busting.

Для лучшей читаемости списка пакетов и избежания потенциального дублирования названия пакетов стоит указывать на отдельных строках и в алфавитном порядке.

```Dockerfile
FROM ubuntu

RUN apt-get update && apt-get install -y \
    python \
    python-dev \
    python3-pip

RUN pip3 install flask flask-mysql

COPY app.py /opt/source-code

ENTRYPOINT flask run
```

### Version Pinning

Можно указать определенную версию пакета:

```Dockerfile
FROM ubuntu

RUN apt-get update && apt-get install -y \
    python \
    python-dev \
    python3-pip=20.0.2

RUN pip3 install flask flask-mysql

COPY app.py /opt/source-code

ENTRYPOINT flask run
```

Также важно обращать внимание на порядок инструкций в Dockerfile. В нашем примере инструкция `COPY` находится практически в самом конце, т.к. мы довольно часто меняем содержимое файла `app.py` в процессе разработки. И в этом случае пересобирать потребуется только последние два слоя.

А что будет если мы перенесем инструкцию `COPY` в начало Dockerfile?

```Dockerfile
FROM ubuntu

COPY app.py /opt/source-code

RUN apt-get update && apt-get install -y \
    python \
    python-dev \
    python3-pip=20.0.2

RUN pip3 install flask flask-mysql

ENTRYPOINT flask run
```

В этом случае при изменении содержимого файла `app.py` rebuild коснется всех нижестоящих слоёв (в том числе установки пакетов и зависимостей), что существенно увеличит время сборки и нагрузку на CPU.

Which option can be used to disable the cache while building a docker image? `--no-cache`

Смотреть информацию сколько дискового пространства занимает build cache:

```shell
$ docker buildx du
$ docker buildx du --verbose
```

Удалить build cache:

```shell
$ docker buildx prune -a
```