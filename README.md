Домашнее задание к занятию "6.1. Типы и структура СУБД"


Задача 1
Архитектор ПО решил проконсультироваться у вас, какой тип БД лучше выбрать для хранения определенных данных.

Он вам предоставил следующие типы сущностей, которые нужно будет хранить в БД:

Электронные чеки в json виде
Склады и автомобильные дороги для логистической компании
Генеалогические деревья
Кэш идентификаторов клиентов с ограниченным временем жизни для движка аутенфикации
Отношения клиент-покупка для интернет-магазина
Выберите подходящие типы СУБД для каждой сущности и объясните свой выбор.

Ответ: 
1. MongoDB - система управления базами данных, которая работает с документоориентированной моделью данных. Отличто подойдет для json файлов;
2. ;
3. ;
4. Redis - СУБД системы ключ-значение, есть параметр Time-To-Live, время жизни запси. Идиальный вариант для этого;
5.  MySql самая популярная БД для интернет-магазинов. Удобно работать с php и PMA.

Задача 2
Вы создали распределенное высоконагруженное приложение и хотите классифицировать его согласно CAP-теореме. Какой классификации по CAP-теореме соответствует ваша система, если (каждый пункт - это отдельная реализация вашей системы и для каждого пункта надо привести классификацию):

Данные записываются на все узлы с задержкой до часа (асинхронная запись)
При сетевых сбоях, система может разделиться на 2 раздельных кластера
Система может не прислать корректный ответ или сбросить соединение
А согласно PACELC-теореме, как бы вы классифицировали данные реализации?

Ответ:

Задача 3
Могут ли в одной системе сочетаться принципы BASE и ACID? Почему?

Ответ:

Задача 4
Вам дали задачу написать системное решение, основой которого бы послужили:

фиксация некоторых значений с временем жизни
реакция на истечение таймаута
Вы слышали о key-value хранилище, которое имеет механизм Pub/Sub. Что это за система? Какие минусы выбора данной системы?

Ответ:
