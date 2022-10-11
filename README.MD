Задача 1
Сценарий выполения задачи:

создайте свой репозиторий на https://hub.docker.com;
выберете любой образ, который содержит веб-сервер Nginx;
создайте свой fork образа;
реализуйте функциональность: запуск веб-сервера в фоне с индекс-страницей, содержащей HTML-код 
Опубликуйте созданный форк в своем репозитории и предоставьте ответ в виде ссылки на https://hub.docker.com/username_repo.


Ответ: https://hub.docker.com/repository/docker/lugy1/nginx_lug


Задача 2
Посмотрите на сценарий ниже и ответьте на вопрос: "Подходит ли в этом сценарии использование Docker контейнеров или лучше подойдет виртуальная машина, физическая машина? Может быть возможны разные варианты?"

Детально опишите и обоснуйте свой выбор.

--

Сценарий:

1. Высоконагруженное монолитное java веб-приложение - приложение монолитное и высоконагруженное, я бы использовал виртуальную машину, чтобы избежать потери ресурсов.

2. Nodejs веб-приложение - контейнеризация подойдет для решения задачи, так как по сути это микро сервисная архитектура

3. Мобильное приложение c версиями для Android и iOS - использование Docker. Обычно мобильная разработка - это всегда обилие настроенного окружения для качественной среды разработки и тестирования.

4. Шина данных на базе Apache Kafka - контейнеризация то что нужно, высоких нагрузок нет, необходимость настройки контейнера из минимум трех компонентов среды

5. Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana - контейниризация на Docker, комфортное создание среды логирования.

6. Мониторинг-стек на базе Prometheus и Grafana - поместить в Docker: сервер Prometheus для сбора метрик, Grafana для визуализации и Node Exporter для мониторинга хост-системы.

7. MongoDB, как основное хранилище данных для java-приложения - виртуальная машина, хотя если нагрузка не будет высока, то возможна и контейнеризация

8. Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry - виртуальный сервер, контейниризация не подойдёт. Физический сервер - возможно слишком много для данной работы.



Задача 3
Запустите первый контейнер из образа centos c любым тэгом в фоновом режиме, подключив папку /data из текущей рабочей директории на хостовой машине в /data контейнера;
Запустите второй контейнер из образа debian в фоновом режиме, подключив папку /data из текущей рабочей директории на хостовой машине в /data контейнера;
Подключитесь к первому контейнеру с помощью docker exec и создайте текстовый файл любого содержания в /data;
Добавьте еще один файл в папку /data на хостовой машине;
Подключитесь во второй контейнер и отобразите листинг и содержание файлов в /data контейнера.

Ответ:
lugy@lugy-virtual-machine:~$ sudo docker run -d -it -v "/home/lugy/data:/data" centos

7451d90b1b4dd8bff95ecb2ab3a23920e824d4c6398520a506d6e304f0114a9a

lugy@lugy-virtual-machine:~$ sudo docker run -d -it -v "/home/lugy/data:/data" debian

d5c98f5824784eb19012512af6df2654babeded2873270ef40dcaf881598242d

lugy@lugy-virtual-machine:~$ sudo docker ps

CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS          PORTS                                   NAMES
d5c98f582478   debian    "bash"                   39 seconds ago       Up 30 seconds                                           lucid_mclaren
7451d90b1b4d   centos    "/bin/bash"              About a minute ago   Up 52 seconds                                           angry_feistel
lugy@lugy-virtual-machine:~$ sudo docker exec -it 7451d90b1b4d /bin/bash

[root@7451d90b1b4d /]# ls
bin  data  dev	etc  home  lib	lib64  lost+found  media  mnt  opt  proc  root	run  sbin  srv	sys  tmp  usr  var

[root@7451d90b1b4d /]# cd /data

[root@7451d90b1b4d data]# vi file_centos.txt

[root@7451d90b1b4d data]# ls

file_centos.txt  term_f.txt

[root@7451d90b1b4d data]# exit

exit
lugy@lugy-virtual-machine:~$ sudo docker exec -it d5c98f582478 /bin/bash
root@d5c98f582478:/# ls
bin  boot  data  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@d5c98f582478:/# cd /data
root@d5c98f582478:/data# ls -l
total 8
-rw-r--r-- 1 root root  8 Oct 11 09:48 file_centos.txt
-rw-rw-r-- 1 1000 1000 14 Oct 11 09:20 term_f.txt
root@d5c98f582478:/data# 




