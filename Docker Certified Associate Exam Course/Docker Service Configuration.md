Docker можно запустить вручную с помощью команды: `sudo dockerd` (предварительно необходимо остановить его через `systemctl`).

Для подробного debug-лога запуска демона: `sudo dockerd --debug`.

По умолчанию Docker слушает только внутренний unix-socket `/var/run/docker.sock`.

Unix-socket - inter-process communication (IPC) mechanism, используется для взаимодействия между различными процессами на одном хосте. Это значит, что Docker Daemon доступен только в пределах самого хоста и Docker CLI настроена для взаимодействия с демоном через сокет.

Но как быть, если нам нужно сделать доступным Docker Daemon извне? Т.е. представим ситуацию, когда Docker CLI установлена на одном, а Docker Daemon на другом.

Для этого запустим Docker Daemon с флагом: `sudo dockerd --debug --host=tcp://192.168.56.104:2375`.

На другом хоста зададим переменную: `export DOCKER_HOST="tcp://192.168.56.104:2375"`. И выполним команду: `docker ps`.

Однако выставлять Docker Daemon наружу следуют с большой осторожностью, т.к. по умолчанию соединение не зашифровано и не требует аутентификации.

Для включения TLS добавим соответствующие флаги:

```shell
sudo dockerd --debug \
             --host=tcp://192.168.56.104:2376 \
             --tls=true \
             --tlscert=/var/docker/server.pem \
             --tlskey=/var/docker/serverkey.pem
```

Обратите внимание, что в случае использования TLS порт указан `2376`.

Можно перенести все эти настройки в файл `/etc/docker/daemon.json` (по умолчанию не создается).

```json
{
    "debug": true,
    "hosts": ["tcp://192.168.56.104:2376"],
    "tls": true,
    "tlscert": "/var/docker/server.pem",
    "tlskey": "/var/docker/serverkey.pem"
}
```