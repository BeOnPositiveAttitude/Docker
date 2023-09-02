```bash
FROM ubuntu
# часто при билде образов на основе Debian или Ubuntu можно увидеть в логе ошибку: "unable to initialize frontend: Dialog"
# это не останавливает билд образа, но говорит о том, что система хотела открыть диалог с пользователем, но не смогла
# для мягкого игнорирования этой ошибки используем следующую переменную
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update
RUN apt-get install -y apache2
# меняем порт со стандартного на кастомный, для этого есть специальный файл ports.conf
RUN sed -i "s/80/3001/g" /etc/apache2/ports.conf
EXPOSE 3001
# запускаем apache2
CMD [ "/usr/sbin/apache2ctl", "-D", "FOREGROUND", "-k", "start" ]
# либо короче
CMD [ "/usr/sbin/apache2ctl", "-D", "FOREGROUND" ]
# важно не использовать одинарные ковычки!!! с ними не сработает запуск apache2
```

```bash
FROM node:alpine
WORKDIR /node_app
COPY package.json server.js ./
# установка зависимостей из файла package.json
RUN npm install
EXPOSE 8083
CMD [ "node", "server.js" ]
```

```bash
FROM python:alpine
WORKDIR /python_app
# Dockerfile лежит внутри каталога с таким же названием /python_app на docker-хосте, а файлы requirements.txt и server.py внутри каталога /python_app/src
# копируем их в образ в каталог /python_app
COPY ./src/* ./
RUN pip3 install -r requirements.txt
EXPOSE 8085
CMD [ "python3", "server.py" ]
```
