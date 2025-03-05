Предположим мы запустили контейнер с httpd и внесли в него некоторые изменения:

```shell
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
$ docker container commit -a "Aidar" apache httpd_custom
```

- `a = author` - автор коммита
- `apache` - название контейнера, с которого делаем образ
- `httpd_custom` - название образа

Данный способ НЕ рекомендуется для использования в production, т.к. поддерживать образы, созданные таким способом, крайне затруднительно. Лучше использовать Dockerfile.