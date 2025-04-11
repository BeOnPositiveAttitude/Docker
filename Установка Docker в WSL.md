### On Windows

Устанавливаем Ubuntu:

```shell
$ wsl --install --distribution ubuntu
# или более коротко
$ wsl --install -d ubuntu
```

Смотреть список доступных дистрибутивов Linux:

```shell
$ wsl --list --online
# или более коротко
$ wsl -l -o
```

Выполняем первичную инициализацию (создаем пользователя, задаем пароль, потом `exit`):

```shell
$ wsl
```

Останавливаем wsl, чтобы отпустить файл образа:

```shell
$ wsl --shutdown
```

Перемещаем `.vhdx`-файл диска ВМ из профиля пользователя в понятное место:

```shell
$ wsl --manage ubuntu --move C:\Tools\WSL\Ubuntu
```

По умолчанию диск лежит в профиле пользователя `C:\Users\%USER%\AppData\Local\wsl\{GUID}`.

Отключаем поддержку графических приложений Linux (WSLg). При запуске wsl-команд из Windows будет меньше мелькать.

Создаем файл `С:\Users\%USER%\.wslconfig` с содержимым:

```shell
[wsl2]
guiApplications=false
```

Настройки из этого файла применяются глобально ко всем дистрибутивам.

### On Linux

Автоматически запускаем wsl под пользователем `root`:

Либо из Windows:

```shell
$ wsl --manage ubuntu --set-default-user root
```

Либо в Linux в файле `/etc/wsl.conf` добавляем:

```shell
[user]
default=root
```

Монтируем диски Windows в пути вида `/c/` вместо `/mnt/c/` (не стал делать). Для этого в файле `/etc/wsl.conf` добавляем:

```shell
[automount]
root=/
```

Отключаем службы, привязанные к платным услугам Canonical или реализующие их централизованное управление:

```shell
$ systemctl disable wsl-pro
$ systemctl disable landscape-client
```
Отключаем графический режим в Ubuntu, соответственно меньше сервисов, быстрее запускается:

```shell
$ systemctl set-default multi-user.target
```

Настраиваем, чтобы wsl (и docker) не останавливались после закрытия консоли с wsl. Для этого создаем файл `/etc/systemd/system/keepalive.service` с содержимым:

```shell
[Unit]
Description=Keep Distro Alive

[Service]
# clean up the waiting signal previous set for
ExecStartPre=/c/windows/system32/waitfor.exe /si KeepUbuntuAlive
# wait indefinitely for the signal
ExecStart=/c/windows/system32/waitfor.exe KeepUbuntuAlive

[Install]
WantedBy=multi-user.target
```

И включаем его в автозапуск:

```shell
$ systemctl enable keepalive.service
```

Обновляем список пакетов, доступных для установки, и устанавливаем обновления системы:

```shell
$ apt update
$ apt upgrade
```

Устанавливаем docker:

```shell
$ apt install docker.io docker-compose-v2
```

Включаем автозапуск демона docker при старте wsl:

```shell
$ systemctl enable docker.service
```

После каждой перезагрузки Windows, чтобы заработал docker, нужно будет один раз запустить wsl, потом его консоль можно закрыть.

### Настраиваем docker в Linux

Меняем подсеть docker (OpenVPN тоже использует `172.x.x.x`). Для этого создаем или меняем файл `/etc/docker/daemon.json`:

```json
{
  "default-address-pools": [
    { "base": "192.168.20.0/24", "size": 26 }
  ]
}
```

While the `base` is the total ip range docker will manage, the `size` defines the netmask of a subnet for each single network that docker can create within the specified (total) ip range.

Включаем управление по порту 2735 (для Windows). Для этого редактируем файл `/etc/docker/daemon.json`, добавляя узел:

```json
{
  "default-address-pools": [
    { "base": "192.168.20.0/24", "size": 26 }
  ],
  "hosts": ["tcp://127.0.0.1:2375", "unix:///var/run/docker.sock"]
}
```

Чтобы docker после этого запускался, убираем ключ `-H`  из команды его запуска в systemd и отключаем навязчивое требование tls. Для этого редактируем сервис `/usr/lib/systemd/system/docker.service`:

```shell
...
ExecStart=/usr/bin/dockerd --tls=false --containerd=/run/containerd/containerd.sock
...
```

### Настраиваем docker в Windows

Скачиваем последнюю версию docker из https://download.docker.com/win/static/stable/x86_64/, распаковываем и кладем `docker.exe` в каталог в `%PATH%`.

Говорим как подключаться к демону docker в wsl. Для этого устанавливаем переменную (действует после перезапуска консоли):

```shell
$ setx DOCKER_HOST tcp://127.0.0.1:2375
```

Скачиваем последнюю версию compose из https://github.com/docker/compose/releases, переименовываем в `docker-compose.exe` и кладем в `C:\Users\%USER%\.docker\cli-plugins\`.

Чтобы compose конвертировал windows-пути в linux-пути для целей монтирования каталога устанавливаем переменную:

```shell
$ setx COMPOSE_CONVERT_WINDOWS_PATHS 1
```