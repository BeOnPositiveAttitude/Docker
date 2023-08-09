```bash
FROM ubuntu
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update
RUN apt-get install -y apache2
RUN sed -i 's/80/3001/g' /etc/apache2/ports.conf
EXPOSE 3001
CMD [ '/usr/sbin/apache2ctl', '-D', 'FOREGROUND', '-k', 'start' ]
```
