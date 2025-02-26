Как приостановить процесс в Linux?

```shell
kill -SIGSTOP $(pgrep httpd)
```

Для возобновления работы процесса нужно послать другой сигнал:

```shell
kill -SIGCONT $(pgrep httpd)
```

Чтобы "мягко" завершить работу процесса:

```shell
kill -SIGTERM $(pgrep httpd)
```

Чтобы "жестко" завершить работу процесса:

```shell
kill -SIGKILL $(pgrep httpd)
```

Вернемся к контейнерам. Чтобы приостановить работу контейнера:

```shell
docker container pause web
```

Чтобы возобновить работу контейнера:

```shell
docker container unpause web
```

Чтобы остановить работу контейнера:

```shell
docker container stop web
```

В случае остановки контейнера Docker сначала посылает сигнал `SIGTERM` для "мягкого" завершения работы. Если же по истечении определенного grace period работа контейнера не была остановлена, тогда посылается сигнал `SIGKILL` для "жесткого" завершения.

В случае же постановки контейнера на паузу Docker НЕ посылает сигналы `SIGSTOP`/`SIGCONT` как можно было бы предположить. В этом случае Docker использует "freezer cgroup".

Также мы можем посылать различные сигналы процессам внутри контейнера с помощью команды:

```shell
docker container kill --signal=9 web
```

Удалить остановленный контейнер:

```shell
docker container rm web
```

При удалении контейнера очищается и его содержимое в каталоге `/var/lib/docker`.

Остановить все запущенные контейнеры:

```shell
docker container stop $(docker container ls -q)
```

Удалить все остановленные контейнеры:

```shell
docker container rm $(docker container ls -qa)
```

Или же для удаления остановленных контейнеров можно воспользоваться командой:

```shell
docker container prune
```

Чтобы контейнер автоматически удалялся при завершении работы:

```shell
docker container run --rm ubuntu expr 4 + 5
```