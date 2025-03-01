Аутентификация в registry:

```shell
$ docker login gcr.io
```

"Переименовать" образ:

```shell
$ docker image tag httpd:alpine gcr.io/company/httpd:customv1
```

Будет создан soft-link на исходный образ. Таким образом не происходит фактического копирования образа, что позволяет экономить дисковое пространство.

Смотреть сколько места занимают различные объекты Docker:

```shell
$ docker system df
TYPE            TOTAL     ACTIVE    SIZE      RECLAIMABLE
Images          33        2         15.89GB   15.34GB (96%)
Containers      4         0         291B      291B (100%)
Local Volumes   8         0         771MB     771MB (100%)
Build Cache     208       0         3.829GB   3.829GB
```

Поле `RECLAIMABLE` показывает сколько места на диске можно освободить, удалив данный тип объектов. В данном случае не 100%, т.к. два образа используются контейнерами и их нельзя удалить.