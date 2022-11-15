Задача 1
В этом задании вы потренируетесь в:

установке elasticsearch
первоначальном конфигурировании elastcisearch
запуске elasticsearch в docker
Используя докер образ centos:7 как базовый и документацию по установке и запуску Elastcisearch:

составьте Dockerfile-манифест для elasticsearch
соберите docker-образ и сделайте push в ваш docker.io репозиторий
запустите контейнер из получившегося образа и выполните запрос пути / c хост-машины
Требования к elasticsearch.yml:

данные path должны сохраняться в /var/lib
имя ноды должно быть netology_test
В ответе приведите:

текст Dockerfile манифеста
ссылку на образ в репозитории dockerhub
ответ elasticsearch на запрос пути / в json виде
Подсказки:

возможно вам понадобится установка пакета perl-Digest-SHA для корректной работы пакета shasum
при сетевых проблемах внимательно изучите кластерные и сетевые настройки в elasticsearch.yml
при некоторых проблемах вам поможет docker директива ulimit
elasticsearch в логах обычно описывает проблему и пути ее решения
Далее мы будем работать с данным экземпляром elasticsearch.

Ответ:

#6.5. Elasticsearch
FROM centos:7
ENV PATH=/usr/lib:/usr/lib/jvm/jre-11/bin:$PATH
RUN yum install java-11-openjdk -y 
RUN yum install wget -y 
RUN wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.11.1-linux-x86_64.tar.gz \
    && wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.11.1-linux-x86_64.tar.gz.sha512 
RUN yum install perl-Digest-SHA -y 
RUN shasum -a 512 -c elasticsearch-7.11.1-linux-x86_64.tar.gz.sha512 \ 
    && tar -xzf elasticsearch-7.11.1-linux-x86_64.tar.gz \
    && yum upgrade -y
ADD elasticsearch.yml /elasticsearch-7.11.1/config/
ENV JAVA_HOME=/elasticsearch-7.11.1/jdk/
ENV ES_HOME=/elasticsearch-7.11.1
RUN groupadd elasticsearch \
    && useradd -g elasticsearch elasticsearch
RUN mkdir /var/lib/logs \
    && chown elasticsearch:elasticsearch /var/lib/logs \
    && mkdir /var/lib/data \
    && chown elasticsearch:elasticsearch /var/lib/data \
    && chown -R elasticsearch:elasticsearch /elasticsearch-7.11.1/
RUN mkdir /elasticsearch-7.11.1/snapshots &&\
    chown elasticsearch:elasticsearch /elasticsearch-7.11.1/snapshots
USER elasticsearch
CMD ["/usr/sbin/init"]
CMD ["/elasticsearch-7.11.1/bin/elasticsearch"]

Ссылка https://hub.docker.com/r/lugy1/dockerhub/tags

sh-4.2$ curl -X GET localhost:9200
{
  "name" : "d983826adf51",
  "cluster_name" : "netology_test",
  "cluster_uuid" : "-dEU81irS6KDFX7XiFkDyw",
  "version" : {
    "number" : "7.11.1",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "ff17057114c2199c9c1bbecc727003a907c0db7a",
    "build_date" : "2021-02-15T13:44:09.394032Z",
    "build_snapshot" : false,
    "lucene_version" : "8.7.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
sh-4.2$


Задача 2
В этом задании вы научитесь:

создавать и удалять индексы
изучать состояние кластера
обосновывать причину деградации доступности данных
Ознакомтесь с документацией и добавьте в elasticsearch 3 индекса, в соответствии со таблицей:

Имя	Количество реплик	Количество шард
ind-1	0	1
ind-2	1	2
ind-3	2	4
Получите список индексов и их статусов, используя API и приведите в ответе на задание.

Получите состояние кластера elasticsearch, используя API.

Как вы думаете, почему часть индексов и кластер находится в состоянии yellow?

Удалите все индексы.

Важно

При проектировании кластера elasticsearch нужно корректно рассчитывать количество реплик и шард, иначе возможна потеря данных индексов, вплоть до полной, при деградации системы.

Ответ:

sh-4.2$ curl -X GET 'localhost:9200/_cat/indices?v'
health status index uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   ind-1 7S6yM8S1RLe3NWzayiWycw   1   0          0            0       208b           208b
yellow open   ind-3 Dqshc35bQGer5ZXgyexZ9Q   4   2          0            0       832b           832b
yellow open   ind-2 NVvFJjJfRrCC_JQW-S2Y3A   2   1          0            0       416b           416b


Состояние кластера

sh-4.2$ curl  http://localhost:9200/_cluster/health?pretty
{
  "cluster_name" : "netology_test",
  "status" : "yellow",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 7,
  "active_shards" : 7,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 10,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 41.17647058823529
}

Статус yellow - из за этой строчки "delayed_unassigned_shards" : 0,

Удаление индексов:

sh-4.2$ curl -X DELETE localhost:9200/ind-1?pretty
{
  "acknowledged" : true
}
sh-4.2$ curl -X DELETE localhost:9200/ind-2?pretty
{
  "acknowledged" : true
}
sh-4.2$ curl -X DELETE localhost:9200/ind-3?pretty
{
  "acknowledged" : true
}


Задача 3
В данном задании вы научитесь:

создавать бэкапы данных
восстанавливать индексы из бэкапов
Создайте директорию {путь до корневой директории с elasticsearch в образе}/snapshots.

Используя API зарегистрируйте данную директорию как snapshot repository c именем netology_backup.

Приведите в ответе запрос API и результат вызова API для создания репозитория.

Создайте индекс test с 0 реплик и 1 шардом и приведите в ответе список индексов.

Создайте snapshot состояния кластера elasticsearch.

Приведите в ответе список файлов в директории со snapshotами.

Удалите индекс test и создайте индекс test-2. Приведите в ответе список индексов.

Восстановите состояние кластера elasticsearch из snapshot, созданного ранее.

Приведите в ответе запрос к API восстановления и итоговый список индексов.

Подсказки:

возможно вам понадобится доработать elasticsearch.yml в части директивы path.repo и перезапустить elasticsearch
