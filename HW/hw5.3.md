# Домашнее задание к занятию "5.3. Введение. Экосистема. Архитектура. Жизненный цикл Docker контейнера".

## Задача 1
Сценарий выполнения задачи:
* создайте свой репозиторий на <https://hub.docker.com>;
* выберете любой образ, который содержит веб-сервер Nginx;
* создайте свой fork образа;
* реализуйте функциональность: запуск веб-сервера в фоне с индекс-страницей, содержащей HTML-код ниже:
```html
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I’m DevOps Engineer!</h1>
</body>
</html>
```
Опубликуйте созданный форк в своем репозитории и предоставьте ответ в виде ссылки на 
<https://hub.docker.com/username_repo>.

### Решение:
index.html:
```html
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I’m DevOps Engineer!</h1>
</body>
</html>

```
Dockerfile:
```dockerfile
vagrant@ubuntu-bionic:~/nginx$ cat Dockerfile
FROM nginx:1.21.6-alpine

COPY ./index.html /usr/share/nginx/html

vagrant@ubuntu-bionic:~/nginx$ sudo docker build -t x129/netology/nginx:hw5.3 .
vagrant@ubuntu-bionic:~/nginx$ sudo docker run --name nginx -d -p 8080:80 x129/netology/nginx:hw5.3
e8608a1fa40136ca2ffcd039ba2aa94fb0151553e9e47cb2169053c6a7faa610
vagrant@ubuntu-bionic:~/nginx$ curl http://localhost:8080/
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I’m DevOps Engineer!</h1>
</body>
</html>
vagrant@ubuntu-bionic:~/nginx$ sudo docker tag x129/netology/nginx:hw5.3 x129/nginx-netology
vagrant@ubuntu-bionic:~/nginx$ sudo docker push x129/nginx-netology
Using default tag: latest
The push refers to repository [docker.io/x129/nginx-netology]
fe7a982078cb: Pushed
c0e7c94aefd8: Pushed
d6dd885da0bb: Pushed
a43749efe4ec: Pushed
45b275e8a06d: Pushed
4721bfafc708: Pushed
4fc242d58285: Pushed
latest: digest: sha256:a6ca1ca08ed401fbba9365e9bb9b3e7e6869cfd32481f62ff2607523415af130 size: 1775
vagrant@ubuntu-bionic:~/nginx$
```

Репозиторий: <https://hub.docker.com/r/literis8/nginx-netology/>

## Задача 2
Посмотрите на сценарий ниже и ответьте на вопрос: "Подходит ли в этом сценарии использование Docker контейнеров или 
лучше подойдет виртуальная машина, физическая машина? Может быть возможны разные варианты?"

Детально опишите и обоснуйте свой выбор:
* Высоконагруженное монолитное java веб-приложение;
* Nodejs веб-приложение;
* Мобильное приложение c версиями для Android и iOS;
* Шина данных на базе Apache Kafka;
* Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash
и две ноды kibana;
* Мониторинг-стек на базе Prometheus и Grafana;
* MongoDB, как основное хранилище данных для java-приложения;
* Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry.

### Ответ:
1. **Высоконагруженное монолитное java веб-приложение** - Физическая машина, java имеет свою виртуальную машину jvm и лучше сократить накладные расходы.
2. **Nodejs веб-приложение** - контейнеризация, веб приложения всегда отличный вариант для контейнеризации и разбиения
на микросервисы.
3. **Мобильное приложение c версиями для Android и iOS** - докер, отлично подойдет для тестирования И разработки.
4. **Шина данных на базе Apache Kafka** - docker + compose , позволит быстро поднимать ноды, а так же позволит реализовать хорошую
отказоустойчивость.
5. **Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два 
logstash и две ноды kibana** - docker + compose , позволит быстро поднимать ноды, а так же позволит реализовать хорошую
отказоустойчивость.
6. **Мониторинг-стек на базе Prometheus и Grafana** - docker + compose. Контейнер с Prometheus, контейнер с Grafana и подключенным
внешним волюмом для хранения данных мониторинга.
7. **MongoDB, как основное хранилище данных для java-приложения** - зависит от заргужености базы, физическая или виртуальная машина в продуктив или docker для разработки.
8. **Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry** - Докер. Оптимальное решение
чтобы в рамках CI/CD в пайплайнах в контейнерах осуществлять конфигурирование, сборку, тестирования кода из репозитория.

## Задача 3
* Запустите первый контейнер из образа _**centos**_ c любым тегом в фоновом режиме, подключив папку `/data` из текущей 
рабочей директории на хостовой машине в `/data` контейнера;
* Запустите второй контейнер из образа **_debian_** в фоновом режиме, подключив папку `/data` из текущей рабочей 
директории на хостовой машине в `/data` контейнера;
* Подключитесь к первому контейнеру с помощью `docker exec` и создайте текстовый файл любого содержания в `/data`;
* Добавьте еще один файл в папку `/data` на хостовой машине;
* Подключитесь во второй контейнер и отобразите листинг и содержание файлов в `/data` контейнера.

### Решение:
```shell
vagrant@ubuntu-bionic:~/nginx$ sudo docker run -dit -v ~/data:/data --name centos centos
vagrant@ubuntu-bionic:~/nginx$ sudo docker run -dit -v ~/data:/data --name debian debian
vagrant@ubuntu-bionic:~/nginx$ sudo docker ps
CONTAINER ID   IMAGE                       COMMAND                  CREATED          STATUS          PORTS                                   NAMES
8c8b2437f6a3   debian                      "bash"                   31 seconds ago   Up 29 seconds                                           debian
7b3759db9859   centos                      "/bin/bash"              54 seconds ago   Up 53 seconds                                           centos
e8608a1fa401   x129/netology/nginx:hw5.3   "/docker-entrypoint.…"   14 minutes ago   Up 14 minutes   0.0.0.0:8080->80/tcp, :::8080->80/tcp   nginx

vagrant@ubuntu-bionic:~/nginx$ sudo docker exec -ti centos bash
[root@7b3759db9859 /]# echo "cetnos test file" > /data/centos.file

vagrant@ubuntu-bionic:~/nginx$ echo "host test file" > /home/vagrant/data/host.file

vagrant@ubuntu-bionic:~/nginx$ sudo docker exec -ti debian bash
root@8c8b2437f6a3:/# ls -la /data
total 12
drwxr-xr-x 2 root root 4096 Nov 10 11:09 .
drwxr-xr-x 1 root root 4096 Nov 10 11:04 ..
-rw-r--r-- 1 root root   17 Nov 10 11:11 centos.file
-rw-r--r-- 1 root root    0 Nov 10 11:09 host.file
root@8c8b2437f6a3:/# ls /data
centos.file  host.file
root@8c8b2437f6a3:/# cat /data/centos.file
cetnos test file
root@8c8b2437f6a3:/#
this is host file
```