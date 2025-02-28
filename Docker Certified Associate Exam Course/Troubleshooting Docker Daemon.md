В случае, когда Docker CLI установлена на одном хосте, а Docker Daemon на другом, на клиенте важно не забывать указывать переменную с адресом Docker-хоста:

- `export DOCKER_HOST="tcp://192.168.56.104:2375"` - без использования tls
- `export DOCKER_HOST="tcp://192.168.56.104:2376"` - с использованием tls

Проверяем статус демона: `sudo systemctl status docker.service`.

Проверяем логи: `sudo journalctl -u docker.service`.

Проверяем конфигурационный файл `/etc/docker/daemon.json` (можно включить debug-mode при необходимости).

```json
{
    "debug": true,
    "hosts": ["tcp://192.168.56.104:2376"],
    "tls": true,
    "tlscert": "/var/docker/server.pem",
    "tlskey": "/var/docker/serverkey.pem"
}
```

Перечитать конфигурацию: `sudo systemctl reload docker.service` или `sudo kill -SIGHUP $(pgrep docker)`.

Проверяем свободное место на диске: `df -h`.

Удалить неиспользуемые контейнеры: `docker container prune`.

Удалить неиспользуемые образы: `docker image prune`.

Команда `docker system info` покажет нам включен ли debug-mode.