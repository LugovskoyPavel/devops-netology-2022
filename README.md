Задача 1
Перед выполнением задания ознакомьтесь с документацией по администрированию MongoDB.
Пользователь (разработчик) написал в канал поддержки, что у него уже 3 минуты происходит CRUD операция в MongoDB и её нужно прервать.
Вы как инженер поддержки решили произвести данную операцию:
напишите список операций, которые вы будете производить для остановки запроса пользователя
предложите вариант решения проблемы с долгими (зависающими) запросами в MongoDB


Ответ:
db.currentOp() - позволяет просмотреть все выполняемые в данный момент запросы (кроме текущего пользователя) (db.currentOp({ "$ownOps": true }) - текущий пользователь)

Далее завершить запрос командой db.killop(), где запрос можно завершить по <opid> - идентификатор запроса

Долгие запросы в MongoDB можно решить целым спектром проблем: 

  *Устранение проблем с дефицитом ресурсов
  

  *Диагностика неэффективного выполнения запросов

  *Устранение проблем с неэффективными запросами

  *Для ускорения выборок используйте индексы. (Однако, каждый новый индекс замедляет запись. Большое количество индексов может негативно влиять на производительность при записи.

  *Диагностика наличия блокировок (locks)

  *Устранение проблем с блокировками

  *Диагностика недостатка дискового пространства

  *Устранение проблем с дисковым пространством

 
Задача 2
Перед выполнением задания познакомьтесь с документацией по Redis latency troobleshooting.
Вы запустили инстанс Redis для использования совместно с сервисом, который использует механизм TTL. Причем отношение количества записанных key-value значений к количеству истёкших значений есть величина постоянная и увеличивается пропорционально количеству реплик сервиса.
При масштабировании сервиса до N реплик вы увидели, что:
сначала рост отношения записанных значений к истекшим
Redis блокирует операции записи
Как вы думаете, в чем может быть проблема?

Ответ: 
Возможно проблема в величине TTL, время затрачиваемое на опреацию записи больше, чем время TTL сервиса. Решение - увеличение TTL ( в разумных перделах).
Или проблема в том, что система хранит большое количество долгоживущих ключей, которые хранят в памяти запросы, обрабатываемые по этим ключам. Решение - Уменьшить TTL, перенестти долгоживущие записи в отдельную логическую DB в рамках того же Redis кластера, периодическая очитстка просроченных ключей.

 
Задача 3
Вы подняли базу данных MySQL для использования в гис-системе. При росте количества записей, в таблицах базы, пользователи начали жаловаться на ошибки вида:
InterfaceError: (InterfaceError) 2013: Lost connection to MySQL server during query u'SELECT..... '
Как вы думаете, почему это начало происходить и как локализовать проблему?
Какие пути решения данной проблемы вы можете предложить?

Ответ: 
Причина возникновения - увеличение нагрузки на сервер в связи с увеличением БД. Увеличивается время выполнения запроса - а зачит connect_timeout с сервером может истеч. Также увеличивается количество информации обрабатываемой сервером в ответ на запрос, что требует увеличения ресурсов сервера. Ещё одной причиной может быть некорретно написанный запрос к БД, который с увеличением количества записей стал тормозить систему - в таком случае следует переписать запрос.
error log - инструмент для выявления ошибки, general query log - если error log ошибки не находит
Пути решения - увеличить connect_timeout, увеличить ресурсы сервера. Ограничить количество подключений max_connections



Задача 4
Вы решили перевести гис-систему из задачи 3 на PostgreSQL, так как прочитали в документации, что эта СУБД работает с большим объемом данных лучше, чем MySQL.
После запуска пользователи начали жаловаться, что СУБД время от времени становится недоступной. В dmesg вы видите, что:
postmaster invoked oom-killer
Как вы думаете, что происходит?
Как бы вы решили данную проблему?

Ответ:
