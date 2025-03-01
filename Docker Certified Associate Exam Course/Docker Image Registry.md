В Docker Hub образы делятся на три категории:

- *Official Images* - поддерживаются непосредственно командой Docker
- *Verified Images* - поддерживаются third-party publishers (Oracle, RedHat и т.д.)
- *User Images* - загруженные обычными пользователями

Смотреть список доступных образов на Docker-хосте:

```shell
docker image ls
```

Искать образ в docker registry. По умолчанию отображаются первые 25 найденных результатов.

```shell
$ docker search httpd
NAME                     DESCRIPTION                                     STARS     OFFICIAL
httpd                    The Apache HTTP Server Project                  4838      [OK]
manageiq/httpd           Container with httpd, built on CentOS for Ma…   1
paketobuildpacks/httpd                                                   0
vulhub/httpd                                                             0
jitesoft/httpd           Apache httpd on Alpine linux.                   0
openquantumsafe/httpd    Demo of post-quantum cryptography in Apache …   17
openeuler/httpd                                                          0
dockerpinata/httpd                                                       1
centos/httpd                                                             36
e2eteam/httpd                                                            0
manasip/httpd                                                            0
amd64/httpd              The Apache HTTP Server Project                  1
futdryt/httpd                                                            0
ppc64le/httpd            The Apache HTTP Server Project                  0
arm64v8/httpd            The Apache HTTP Server Project                  11
9af925e7043/httpd                                                        0
arm32v7/httpd            The Apache HTTP Server Project                  11
s390x/httpd              The Apache HTTP Server Project                  1
signiant/httpd           httpd (apache2) base container with a custom…   0
tugboatqa/httpd          The Apache HTTP Server Project                  0
i386/httpd               The Apache HTTP Server Project                  1
publici/httpd            httpd:latest                                    1
inventis/httpd           apache container with support for https only    0
armhf/httpd              The Apache HTTP Server Project                  8
vzwingmadomatic/httpd    Service frontal de l'application de domotique   0
```

Можно увеличить либо уменьшить это значение при поиске:

```shell
$ docker search httpd --limit=2
NAME             DESCRIPTION                                     STARS     OFFICIAL
httpd            The Apache HTTP Server Project                  4838      [OK]
manageiq/httpd   Container with httpd, built on CentOS for Ma…   1
```

Также при поиске можно использовать различные фильтры. Например искать образы, у которых есть минимум 10 звезд:

```shell
$ docker search --filter stars=10 httpd
NAME                    DESCRIPTION                                     STARS     OFFICIAL
httpd                   The Apache HTTP Server Project                  4838      [OK]
openquantumsafe/httpd   Demo of post-quantum cryptography in Apache …   17
centos/httpd                                                            36
arm64v8/httpd           The Apache HTTP Server Project                  11
arm32v7/httpd           The Apache HTTP Server Project                  11
```

Искать образы, у которых есть минимум 10 звезд и метка Official Publisher:

```shell
$ docker search --filter stars=10 --filter is-official=true httpd
NAME      DESCRIPTION                      STARS     OFFICIAL
httpd     The Apache HTTP Server Project   4838      [OK]
```

Загрузить образ:

```shell
$ docker image pull httpd
```