### Преимущества команды `ADD` (или чего не умеет `COPY`)

В данном примере содержимое архива будет распаковано в образ (а не просто копирование архива в образ как может показаться на первый взгляд):

```Dockerfile
FROM redhat/ubi9
ADD app.tar.xz /testdir
```

С помощью `ADD` можно скачать файл по ссылке:

```Dockerfile
FROM redhat/ubi9
ADD http://app.tar.xz /testdir
RUN tar -xJf /testdir/app.tar.xz -C /tmp/app
RUN make -C /tmp/app
```

Однако best practice говорят, что по возможности следует использовать `COPY`, т.к. из названия команды сразу понятно, что она делает. В этом плане `ADD` не так очевидна, судя по первому примеру.

Если посмотреть второй пример, то каждая новая инструкция создает новый слой в образе и тем самым увеличивает его размер. Поэтому лучше переписать в одну инструкцию:

```Dockerfile
FROM redhat/ubi9
RUN curl http://app.tar.xz \
    | tar -xcJ /testdir/file.tar.xz \
    && yarn build \
    && rm /testdir/file.tar.xz
```