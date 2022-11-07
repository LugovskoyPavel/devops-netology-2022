# Домашнее задание к занятию "6.4. PostgreSQL"

Задача 1

Используя docker поднимите инстанс PostgreSQL (версию 13). Данные БД сохраните в volume.
Подключитесь к БД PostgreSQL используя psql.
Воспользуйтесь командой \? для вывода подсказки по имеющимся в psql управляющим командам.
Найдите и приведите управляющие команды для:
вывода списка БД
подключения к БД
вывода списка таблиц
вывода описания содержимого таблиц
выхода из psql

Ответ:
вывода списка БД - \l[+]
подключения к БД - \c (\connect)
вывода списка таблиц - \dt
вывода описания содержимого таблиц - \d[S+]
выхода из psql - \q



Задача 2
Используя psql создайте БД test_database.
Изучите бэкап БД.
Восстановите бэкап БД в test_database.
Перейдите в управляющую консоль psql внутри контейнера.
Подключитесь к восстановленной БД и проведите операцию ANALYZE для сбора статистики по таблице.
Используя таблицу pg_stats, найдите столбец таблицы orders с наибольшим средним значением размера элементов в байтах.
Приведите в ответе команду, которую вы использовали для вычисления и полученный результат.

Ответ:

test_database=# SELECT attname, avg_width FROM pg_stats WHERE tablename = 'orders' order by avg_width desc LIMIT 1;


attname | avg_width 
---------+-----------
 title   |        16
(1 row)



Задача 3
Архитектор и администратор БД выяснили, что ваша таблица orders разрослась до невиданных размеров и поиск по ней занимает долгое время. Вам, как успешному выпускнику курсов DevOps в нетологии предложили провести разбиение таблицы на 2 (шардировать на orders_1 - price>499 и orders_2 - price<=499).
Предложите SQL-транзакцию для проведения данной операции.
Можно ли было изначально исключить "ручное" разбиение при проектировании таблицы orders?

Ответ:
BEGIN;
CREATE TABLE orders_1 (CHECK (price > 499)) INHERITS (orders);
CREATE TABLE orders_2 (CHECK (price<=499)) INHERITS (orders);
CREATE RULE rl1 AS ON INSERT TO orders WHERE price > 499 DO INSTEAD INSERT INTO orders_1 VALUES (NEW.*);
CREATE RULE rl2 AS ON INSERT TO orders WHERE price<=499 DO INSTEAD INSERT INTO orders_2 VALUES (NEW.*);
COMMIT;

И два правила для добавления новых данных в таблицу orders

Можно ли было изначально исключить “ручное” разбиение при проектировании таблицы orders?
Ответ - можно, если знать, что по каккому-то столюцу таблицы записи будут распределяться равномерно. 
Тогда можно разделить таблицы исходя по определенным диапазонам на достаточно равные подтаблицы.

Задача 4
Используя утилиту pg_dump создайте бекап БД test_database.
Как бы вы доработали бэкап-файл, чтобы добавить уникальность значения столбца title для таблиц test_database?

Ответ:

postgres@25b5dcb63da7:~$ pg_dump test_database > lps_dump.sql

Можно доработать бекап-файл базы данных добавив индекс или первичный ключ для title.
  
