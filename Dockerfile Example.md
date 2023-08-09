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
