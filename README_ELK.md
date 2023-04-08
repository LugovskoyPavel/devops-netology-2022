# Домашнее задание к занятию 15 «Система сбора логов Elastic Stack»

## Задание 1

Вам необходимо поднять в докере и связать между собой:

- elasticsearch (hot и warm ноды);
- logstash;
- kibana;
- filebeat.

Logstash следует сконфигурировать для приёма по tcp json-сообщений.

Filebeat следует сконфигурировать для отправки логов docker вашей системы в logstash.

В директории [help](./help) находится манифест docker-compose и конфигурации filebeat/logstash для быстрого 
выполнения этого задания.

Результатом выполнения задания должны быть:

- скриншот `docker ps` через 5 минут после старта всех контейнеров (их должно быть 5);
- скриншот интерфейса kibana;
- docker-compose манифест (если вы не использовали директорию help);
- ваши yml-конфигурации для стека (если вы не использовали директорию help).

Ответ:

Docker поднял на Windows. Пришлось помучится с ошибками vm.max_map_count и root при запуске filebeat

![docker_elk](https://user-images.githubusercontent.com/104651372/230715028-9bb602ce-1e8b-407e-a19f-821a1f7f62af.png)

![kibana](https://user-images.githubusercontent.com/104651372/230715018-b4d5e117-aa20-4f25-a1f5-87f1d2a3bad1.png)

## Задание 2

Перейдите в меню [создания index-patterns  в kibana](http://localhost:5601/app/management/kibana/indexPatterns/create) и создайте несколько index-patterns из имеющихся.

Перейдите в меню просмотра логов в kibana (Discover) и самостоятельно изучите, как отображаются логи и как производить поиск по логам.

В манифесте директории help также приведенно dummy-приложение, которое генерирует рандомные события в stdout-контейнера.
Эти логи должны порождать индекс logstash-* в elasticsearch. Если этого индекса нет — воспользуйтесь советами и источниками из раздела «Дополнительные ссылки» этого задания.
 
Ответ:

![index_kibana](https://user-images.githubusercontent.com/104651372/230723923-0d1926d4-7339-468a-83c0-4765cd336097.png)

![discover_kibana](https://user-images.githubusercontent.com/104651372/230723932-d074838f-42d4-4b5e-a916-5eb63d3eabdf.png)


 
