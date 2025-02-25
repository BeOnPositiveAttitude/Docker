Запусти контейнер с ubuntu и подключимся к его терминалу:

```shell
docker container run -it ubuntu
```

Чтобы выйти из shell, но не погасив при этом контейнер нужно нажать комбинацию клавиш - `Ctrl`+`p`+`q`.

Выполнить команду на запущенном контейнере:

```shell
$ docker container exec custom-webapp hostname
56309afe0732
```

Подключиться к запущенному контейнеру:

```shell
docker container exec -it custom-webapp /bin/bash
```

### Difference Between Docker Exec and Docker Attach

The primary difference between the `docker exec` and `docker attach` command is that the `docker exec` executes a command in a new process. It's typically used for starting an interactive shell in a running container, or to execute arbitrary commands in the container environment.

On the other hand, the `docker attach` command does not start any new process. It attaches the current terminal's standard input and output streams to the primary process of the running containers. The primary usage of the `docker attach` command is when we want to send commands to the primary process directly.