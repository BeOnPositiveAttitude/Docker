Если не задано никаких ограничений процесс (контейнер) может утилизировать всю доступную физическую память на хосте. После того как вся физическая память исчерпана Linux начинает использовать swap-пространство на диске.

Когда в системе не осталось доступной памяти, ОС "выкидывает" OOM exception (Out of Memory) и начинает убивать процессы для высвобождения памяти.

Можно задать ограничение потребления памяти контейнером при запуске:

```shell
$ docker container run --memory=512m ubuntu
```

Данная запись говорит о том, что контейнер может употребить 512 Мб RAM, а при превышении этого значения **еще 512 Мб swap-пространства**, если оно включено на хосте. То есть суммарно 1 Гб памяти (RAM + swap).

Чтобы задать ограничение потребления swap-пространства контейнером:

```shell
$ docker container run --memory=512m --memory-swap=512m ubuntu
```

Опция `--memory-swap` должна использоваться в связке с опцией `--memory`.

На самом деле опция `--memory-swap` означает сумму размеров памяти RAM и swap-пространства, а не только размер swap-пространства. Таким образом в примере выше размер ограничения на swap-пространство равен 0.

Если мы хотим задать размер ограничения swap-пространства равным 256 Мб:

```shell
$ docker container run --memory=512m --memory-swap=768m ubuntu   # 768 - 512 = 256
```

Если контейнер попытается выйти за рамки лимита по CPU, система ограничит (throttle) его потребление заданными рамками.

Что касается RAM, то контейнер может выйти за установленный лимит по памяти, но в таком случае он будет остановлен (terminated) с ошибкой OOM (Out of Memory).

```shell
$ docker container run -itd --rm --name=testone ubuntu
$ docker container inspect testone -f '{{ .HostConfig.Memory }}'
0   # нет ограничений на RAM

$ docker container run -itd --rm --name=testtwo --memory=200m ubuntu
$ docker container inspect testtwo -f '{{ .HostConfig.Memory }}'
209715200

$ docker container run -itd --rm --name=testthree --memory=200m --memory-swap=-1 ubuntu   # swap неограничен в потреблении
$ docker container inspect testthree -f '{{ .HostConfig.Memory }} {{ .HostConfig.MemorySwap }}'
209715200 -1

$ docker container run -itd --rm --name=testfour --memory=200m --memory-swap=-1 --memory-reservation=100m ubuntu
$ docker container inspect testfour -f '{{ .HostConfig.Memory }} {{ .HostConfig.MemorySwap }} {{ .HostConfig.MemoryReservation }}'
209715200 -1 104857600
```

Опция `--memory-reservation` гарантирует выделение запрошенного объема памяти на хосте, похоже на `requests` в K8s.