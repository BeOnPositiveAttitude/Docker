Доступ к приложению, развернутому в контейнере, можно получить несколькими способами.

1. Обратиться непосредственно к IP-адресу контейнера, находясь при этом на Docker-хосте. Однако в этом случае обратиться к приложению извне Docker-хоста не получится.

   ```shell
   $ curl http://172.17.0.2
   <html><body><h1>It works!</h1></body></html>
   ```

2. При запуске контейнера настроить маппинг портов. Таким образом можно будет получить доступ к приложению извне, обратившись к IP-адресу самого Docker-хоста и указав порт.

   ```shell
   $ docker container run -d --rm --name=apache -p 8080:80 httpd
   ```

3. Предположим, что Docker-хост имеет несколько сетевых интерфейсов, которые подключены к разным сетям. По умолчанию обратиться к приложению извне Docker-хоста можно через любой сетевой интерфейс. Но как быть, если мы хотим ограничиться только одной сетью? Для этого при запуске контейнера нужно явно указать IP-адрес сетевого интерфейса:

   ```shell
   $ docker container run -d --rm --name=apache -p 192.168.56.104:8080:80 httpd
   ```

   Можно опубликовать приложение только на localhost:

   ```shell
   $ docker container run -d --rm --name=apache -p 127.0.0.1:8080:80 httpd
   ```

   Если при запуске контейнера мы укажем только порт приложения в контейнере, тогда на Docker-хосте будет выбран рандомный порт для публикации из диапазона `32768 - 60999`:

   ```shell
   $ docker container run -d --rm --name=apache -p 80 httpd

   $ docker container ls
   CONTAINER ID   IMAGE     COMMAND              CREATED         STATUS         PORTS                                       NAMES
   e1ed7f512de3   httpd     "httpd-foreground"   4 seconds ago   Up 4 seconds   0.0.0.0:32768->80/tcp, [::]:32768->80/tcp   apache
   ```

   Этот диапазон портов указан в файле:

   ```shell
   $ cat /proc/sys/net/ipv4/ip_local_port_range
   32768   60999
   ```

4. Что если мы хотим опубликовать приложение без явного указания портов? Предположим наше приложение публикуется сразу на нескольких портах и мы не хотим их явно указывать.

   ```shell
   $ docker container run -d --rm --name=apache -P httpd

   $ docker container ls
   CONTAINER ID   IMAGE     COMMAND              CREATED         STATUS         PORTS                                       NAMES
   ee959f31f230   httpd     "httpd-foreground"   5 seconds ago   Up 5 seconds   0.0.0.0:32769->80/tcp, [::]:32769->80/tcp   apache
   ```

   В этом случае (как и в предыдущем) на Docker-хосте будет выбран рандомный порт из диапазона `32768 - 60999`. А какой порт контейнера нужно опубликовать Docker понимает из инструкции `EXPOSE` в Dockerfile.

   Также можно указать дополнительный порт для публикации, который не указан в инструкции `EXPOSE` в Dockerfile.

   ```shell
   $ docker container run -d --rm --name=apache -P --expose=8080 httpd

   $ docker container ls
   CONTAINER ID   IMAGE     COMMAND              CREATED         STATUS         PORTS                                                                                      NAMES
   5c4f364c44aa   httpd     "httpd-foreground"   6 seconds ago   Up 5 seconds   0.0.0.0:32770->80/tcp, [::]:32770->80/tcp, 0.0.0.0:32771->8080/tcp, [::]:32771->8080/tcp   apache
   ```

   Чтобы узнать какие порты публикуются контейнером, можно заглянуть в вывод команды:

   ```shell
   docker container inspect apache
   ...
   "ExposedPorts": {
      "80/tcp": {},
      "8080/tcp": {}
   },
   ```

Под капотом маппинг портов контейнеров с портами на Docker-хосте происходит с помощью правил iptables.

Docker создает свои собственные цепочки правил iptables - `DOCKER` и `DOCKER-USER`.

Посмотрим на созданные правила:

```shell
$ sudo iptables -t nat -S DOCKER
-N DOCKER
-A DOCKER ! -i docker0 -p tcp -m tcp --dport 32770 -j DNAT --to-destination 172.17.0.2:80
-A DOCKER ! -i docker0 -p tcp -m tcp --dport 32771 -j DNAT --to-destination 172.17.0.2:8080
```

Важно понимать, что при неявной (динамической) публикации портов, например через опцию `-P`, после рестарта контейнера порт на Docker-хосте может поменяться на другой. Это существенный минус.

При статической публикации портов, например через опцию `-p 8080:80`, такой проблемы нет.