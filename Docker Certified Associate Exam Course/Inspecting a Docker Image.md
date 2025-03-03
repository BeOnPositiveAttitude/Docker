Смотреть из каких слоёв состоит образ. Полезно в случае, когда у нас нет доступа к Dockefile, из которого собирался образ.

```
$ docker image history ubuntu
IMAGE          CREATED       CREATED BY                                      SIZE      COMMENT
a04dc4851cbc   5 weeks ago   /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>      5 weeks ago   /bin/sh -c #(nop) ADD file:6df775300d76441aa…   78.1MB
<missing>      5 weeks ago   /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B
<missing>      5 weeks ago   /bin/sh -c #(nop)  LABEL org.opencontainers.…   0B
<missing>      5 weeks ago   /bin/sh -c #(nop)  ARG LAUNCHPAD_BUILD_ARCH     0B
<missing>      5 weeks ago   /bin/sh -c #(nop)  ARG RELEASE                  0B
```

Чтобы узнать больше информации об образе, можно воспользоваться командой `inspect`:

```shell
$ docker image inspect httpd
```

Можно вытащить определенное поле (операционную систему в данном примере):

```shell
$ docker image inspect httpd -f '{{.Os}}'
linux
```

Либо сразу несколько полей:

```shell
$ docker image inspect httpd -f '{{.Architecture}} {{.Os}}'
amd64 linux
```