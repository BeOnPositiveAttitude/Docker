Когда мы устанавливаем Docker, то автоматически создаются три сети:

- **bridge**
- **none**
- **host**

По умолчанию все контейнеры подключаются к сети bridge.

Если требуется подключить контейнер к другой сети, нужно использовать параметр `--network` при запуске:

```shell
$ docker run --network=host ubuntu
```

**bridge** - это внутренняя private сеть, создаваемая Docker-ом на хосте. Контейнеры подключенные к этой сети получают IP-адреса из диапазона `172.17.0.0/16` (адрес шлюза `172.17.0.1`, как правило это интерфейс `docker0`) и могут взаимодействовать друг с другом с помощью этих адресов в случае необходимости.

Чтобы получить доступ извне к контейнеру, подключенному к bridge-сети, необходимо настроить маппинг портов.

Другой способ получить доступ к контейнеру извне - подключить его к **host**-сети, в этом случае маппинг портов не требуется, т.к. порт контейнера автоматически занимает порт на Docker-хосте. Минус данного способа состоит в том, чем мы не можем запустить несколько контейнеров (например с портом 5000) на одном хосте, т.к. порт 5000 уже будет занят первым запущенным контейнером.

При подключении к сети **none** контейнеры не имеют доступа в какую-либо сеть, ни во внешнюю, ни для взаимодействия с другими контейнерами.

Предположим, что нам нужно изолировать контейнеры в рамках одного Docker-хоста и поместить два из них в сеть `172.17.0.1`, а два других в сеть `182.18.0.1`.

По умолчанию Docker создает только одну bridge-сеть.

Мы можем создать новую сеть:

```shell
$ docker network create --driver bridge --subnet 182.18.0.0/16 custom-isolated-network
$ docker network create --driver=bridge --subnet=182.18.0.0/24 --gateway=182.18.0.1 wp-mysql-network
```

Смотреть список сетей:

```shell
$ docker network ls
```

Смотреть детальную информацию о сети

```shell
$ docker network inspect custom-isolated-network
```

Узнать IP-адрес контейнера можно командой:

```shell
$ docker inspect container_name
```

Подключить контейнер к кастомной сети:

```shell
$ docker network connect custom_net_name container_name
```

Один контейнер можно подключить к нескольким сетям.

Отключить контейнер от кастомной сети:

```shell
$ docker network disconnect custom_net_name container_name
```

Удалить сеть:

```shell
$ docker network rm custom_net_name
```

Удалить неиспользуемые сети:

```shell
$ docker network prune
```

Для взаимодействия контейнеров друг с другом не стоит указывать IP-адрес, т.к. он может поменяться после перезагрузки контейнера. В Docker есть встроенный DNS-сервер и контейнеры могут взаимодействовать друг с другом, обращаясь по имени контейнера. Встроенный DNS-сервер запускается с адресом `127.0.0.11`.

Docker использует network namespaces, т.е. создается отдельный namespace для каждого контейнера и использует virtual ethernet pairs для подключения контейнеров друг к другу.

### DNS

Containers use the same DNS servers as the host by default, but you can override this with `--dns`.

By default, containers inherit the DNS settings as defined in the `/etc/resolv.conf` configuration file.

Containers that attach to the default bridge network receive a copy of this file.

Containers that attach to a custom network use Docker's embedded DNS server `127.0.0.11`.

The embedded DNS server forwards external DNS lookups to the DNS servers configured on the host.
