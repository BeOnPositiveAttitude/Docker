Предположим мы запустили контейнер с httpd и внесли в него некоторые изменения:

```
$ docker container run -d --rm --name=apache httpd
d84cb2ab5e19a480a1c5a5cf0be7b60c89c79fcf4447f488a329fe4e615fddb5

$ docker exec -it apache bash
root@d84cb2ab5e19:/usr/local/apache2# cat htdocs/index.html
<html><body><h1>It works!</h1></body></html>

# Запишем в index.html наш кастомный текст (завершить ввод - Ctrl+D)
root@d84cb2ab5e19:/usr/local/apache2# cat > htdocs/index.html
Welcome to my custom web application
```

Далее сохраняем образ через коммит:

```shell
$ docker container commit -a "Aidar Yakhin" apache httpd_custom
```

- `a = author` - автор коммита
- `apache` - название контейнера, с которого делаем образ
- `httpd_custom` - название образа

Данный способ НЕ рекомендуется для использования в production, т.к. поддерживать образы, созданные таким способом, крайне затруднительно. Лучше использовать Dockerfile.

Демо:

```
$ docker container run -itd --name=rh-apache redhat/ubi9
$ docker container exec -it rh-apache /bin/bash
[root@40c530d00a12 /]# dnf update
[root@40c530d00a12 /]# dnf install httpd -y
[root@40c530d00a12 /]# echo "<h1>Hello from KodeKloud</h1>" > /var/www/html/index.html
exit
# останавливаем контейнер перед тем как сделать коммит
$ docker container stop rh-apache
$ docker container commit -a "Aidar Yakhin" -c 'CMD ["httpd", "-D", "FOREGROUND"]' rh-apache webtest:v1
```

`c = change` - apply Dockerfile instruction to the created image

Запустим контейнер из созданного образа и проверим:

```shell
$ docker container run -d --rm --name=rh-apache-test -p 80:80 webtest:v1
```