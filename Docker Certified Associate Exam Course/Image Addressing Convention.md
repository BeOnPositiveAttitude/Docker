Когда мы указываем образ в формате:

```yaml
image: httpd
```

Подразумевается:

```yaml
image: docker.io/httpd/httpd
```

- `docker.io` - адрес registry
- первое `httpd` - user/account (имя пользователя или организации в Docker Hub)
- второе `httpd` - image/repository