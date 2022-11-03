Домашнее задание к занятию "6.2. SQL"

Задача 1
Используя docker поднимите инстанс PostgreSQL (версию 12) c 2 volume, в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose манифест.

Ответ: sudo docker run --name lpspg -e POSTGRES_PASSWORD=21223 -p 5432:5432 -v my-postgres-data:/var/lib/postgresql/data -v my-postgres-backup:/var/lib/postgresql/backup -d postgres
----------------------------------------------------------------------------------------------------------------------------------------
Задача 2
В БД из задачи 1:

создайте пользователя test-admin-user и БД test_db
в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже)
предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db
создайте пользователя test-simple-user
предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE данных таблиц БД test_db
Таблица orders:

id (serial primary key)
наименование (string)
цена (integer)
Таблица clients:

id (serial primary key)
фамилия (string)
страна проживания (string, index)
заказ (foreign key orders)
Приведите:

итоговый список БД после выполнения пунктов выше,
описание таблиц (describe)
SQL-запрос для выдачи списка пользователей с правами над таблицами test_db
список пользователей с правами над таблицами test_db

Ответ: 

test_db=# \l+

                                                                   List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges   |  Size   | Tablespace |                Description                 
-----------+----------+----------+------------+------------+-----------------------+---------+------------+--------------------------------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |                       | 7945 kB | pg_default | default administrative connection database
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +| 7801 kB | pg_default | unmodifiable empty database
           |          |          |            |            | postgres=CTc/postgres |         |            | 
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +| 7801 kB | pg_default | default template for new databases
           |          |          |            |            | postgres=CTc/postgres |         |            | 
 test_db   | postgres | UTF8     | en_US.utf8 | en_US.utf8 |                       | 8097 kB | pg_default | 
(4 rows)


test_db=# \d+ orders
                                                Table "public.orders"
 Column |  Type   | Collation | Nullable |              Default               | Storage  | Stats target | Description 
--------+---------+-----------+----------+------------------------------------+----------+--------------+-------------
 id     | integer |           | not null | nextval('orders_id_seq'::regclass) | plain    |              | 
 name   | text    |           |          |                                    | extended |              | 
 price  | integer |           |          |                                    | plain    |              | 
Indexes:
    "orders_pkey" PRIMARY KEY, btree (id)
Referenced by:
    TABLE "clients" CONSTRAINT "clients_bookings_fkey" FOREIGN KEY (bookings) REFERENCES orders(id)
Access method: heap

test_db=# \d+ clients
                                                 Table "public.clients"
  Column  |  Type   | Collation | Nullable |               Default               | Storage  | Stats target | Description 
----------+---------+-----------+----------+-------------------------------------+----------+--------------+-------------
 id       | integer |           | not null | nextval('clients_id_seq'::regclass) | plain    |              | 
 lastname | text    |           |          |                                     | extended |              | 
 country  | text    |           |          |                                     | extended |              | 
 bookings | integer |           |          |                                     | plain    |              | 
Indexes:
    "clients_pkey" PRIMARY KEY, btree (id)
    "country_index" btree (country)
Foreign-key constraints:
    "clients_bookings_fkey" FOREIGN KEY (bookings) REFERENCES orders(id)
Access method: heap

Список пользователей:

test_db=# SELECT table_name,grantee,privilege_type FROM information_schema.table_privileges WHERE table_schema NOT IN ('information_schema','pg_catalog');
 table_name |     grantee      | privilege_type 
------------+------------------+----------------
 orders     | postgres         | INSERT
 orders     | postgres         | SELECT
 orders     | postgres         | UPDATE
 orders     | postgres         | DELETE
 orders     | postgres         | TRUNCATE
 orders     | postgres         | REFERENCES
 orders     | postgres         | TRIGGER
 clients    | postgres         | INSERT
 clients    | postgres         | SELECT
 clients    | postgres         | UPDATE
 clients    | postgres         | DELETE
 clients    | postgres         | TRUNCATE
 clients    | postgres         | REFERENCES
 clients    | postgres         | TRIGGER
 orders     | test-simple-user | INSERT
 orders     | test-simple-user | SELECT
 orders     | test-simple-user | UPDATE
 orders     | test-simple-user | DELETE
 clients    | test-simple-user | INSERT
 clients    | test-simple-user | SELECT
 clients    | test-simple-user | UPDATE
 clients    | test-simple-user | DELETE
(22 rows)


---------------------

Задача 3
Используя SQL синтаксис - наполните таблицы следующими тестовыми данными:

Таблица orders

Наименование	цена
Шоколад	10
Принтер	3000
Книга	500
Монитор	7000
Гитара	4000
Таблица clients

ФИО	Страна проживания
Иванов Иван Иванович	USA
Петров Петр Петрович	Canada
Иоганн Себастьян Бах	Japan
Ронни Джеймс Дио	Russia
Ritchie Blackmore	Russia
Используя SQL синтаксис:

вычислите количество записей для каждой таблицы
приведите в ответе:
запросы
результаты их выполнения.

Ответ:

test_db=# INSERT INTO orders (name,price) VALUES ('Шоколад',10), ('Принтер',3000), ('Книга',500), ('Монитор',7000), ('Гитара',4000);
INSERT 0 5

test_db=#INSERT INTO clients (lastname,country,bookings) VALUES ('Иванов Иван Иванович','USA',NULL), ('Петров Петр Петрович','Canada',NULL), ('Иоганн Себастьян Бах','Japan',NULL), ('Ронни Джеймс Дио','Russia',NULL), ('Ritchie Blackmore','Russia',NULL);

test_db=# select count(*) from orders;
 count 
-------
     5
(1 row)

***********************************************************

test_db=# select count(*) from clients;
 count 
-------
     5
(1 row)
---------------------------------------------------------------------
Задача 4
Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys свяжите записи из таблиц, согласно таблице:

ФИО	Заказ
Иванов Иван Иванович	Книга
Петров Петр Петрович	Монитор
Иоганн Себастьян Бах	Гитара
Приведите SQL-запросы для выполнения данных операций.

Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод данного запроса.

Подсказк - используйте директиву UPDATE.

Ответ: 

test_db=# UPDATE clients SET bookings = (SELECT id FROM orders WHERE name = 'Книга') WHERE lastname = 'Иванов Иван Иванович';
UPDATE 1
test_db=# UPDATE clients SET bookings = (SELECT id FROM orders WHERE name = 'Монитор') WHERE lastname = 'Петров Петр Петрович';
UPDATE 1
test_db=# UPDATE clients SET bookings = (SELECT id FROM orders WHERE name = 'Гитара') WHERE lastname = 'Иоганн Себастьян Бах';
UPDATE 1

test_db=# select * from clients;
 id |                lastname                | country | bookings 
----+----------------------------------------+---------+----------
  4 | Ронни Джеймс Дио         | Russia  |         
  5 | Ritchie Blackmore                      | Russia  |         
  1 | Иванов Иван Иванович | USA     |        3
  2 | Петров Петр Петрович | Canada  |        4
  3 | Иоганн Себастьян Бах | Japan   |        5
(5 rows)

test_db=# select * from orders;
 id |      name      | price 
----+----------------+-------
  1 | Шоколад |    10
  2 | Принтер |  3000
  3 | Книга     |   500
  4 | Монитор |  7000
  5 | Гитара   |  4000
(5 rows)

Задача 5
Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 (используя директиву EXPLAIN).

Приведите получившийся результат и объясните что значат полученные значения.

Задача 6
Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. Задачу 1).

Остановите контейнер с PostgreSQL (но не удаляйте volumes).

Поднимите новый пустой контейнер с PostgreSQL.

Восстановите БД test_db в новом контейнере.

Приведите список операций, который вы применяли для бэкапа данных и восстановления.
