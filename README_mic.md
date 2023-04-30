# Домашнее задание к занятию «Введение в микросервисы»

## Задача 1: Интернет Магазин

Руководство крупного интернет-магазина, у которого постоянно растёт пользовательская база и количество заказов, рассматривает возможность переделки своей внутренней   ИТ-системы на основе микросервисов. 

Вас пригласили в качестве консультанта для оценки целесообразности перехода на микросервисную архитектуру. 

Опишите, какие выгоды может получить компания от перехода на микросервисную архитектуру и какие проблемы нужно решить в первую очередь.

Ответ:

## Преимущество от внедрения микросервисной архитектуры

1. Сокращение издержек на разработку.
Микросервисная архитектура позволяет сократить временные и часто финансовые издержки при разработке новых функций и модернизации старых в интернет магазине
Микросервисная архитектура позволяет проще оценивать внедрение нового функционала, внедрять новые компоненты.

2. Сокращение издержек на оборудование.
Вполне возможным вариантом внедрения микросервисов может стать использование «мелких» серверов, на каждом из которых будет работать отдельный микросервис, зачастую можно использовать виртуальные сервера

3. Высокая стабильность.
Система, которая разделена на независимые сервисы, является более устойчивой к сбоям и ошибкам. Так как в этом случае, при возникновении сбоя в каком-либо сервисе, сайт будет продолжать работу и возможно даже обрабатывать заказы клиентов.

4. Высокая мобильность к изменениям
При возникновении пиковых нагрузок, например, возможно увеличение ресурсов микросервисов, а при возвращении к обычным нагрузкам – снижение ресурсов. Также возможно достаточно быстро и качественно реагировать на запросы пользователей, убирать и вводить акционные предложения, какие-то маркетинговые решения и прочее.

## Недостатки внедрения микросервисной архитектуры

1. Высокая стоимость
Стоимость разработки микросервисных решений выше чем у обычных монолитных приложений, так как команда разработки зачастую будет стоить дороже

2. Расходы на дополнительное ПО
Потребуются учесть расходы на содержание серверной части и администрирование программного обеспечения: на управление контейнерами, API Gateway и прочие технологии. Эти расходы будут значительно выше, чем расходы на обслуживание простого хостинга

3. Расходы на тестирование
Обязательной частью микросервисной среды является тестирование, и автоматическое тестирование, которое необходимо применять на всех этапах разработки и внедрения сервисов.

4. Расходы на метрики и обратную связь
Обязательное отслеживание состояния сайта и получения обратной связи о тклиентов. Позволит выделить тонкте места в системе, а также предотвратить обрушения в моменты пиковых нагрузок

5. Выбор патерна проектирования
При запуске проекта перехода на микросервисную архитектуру необходимо правильно проанализировать бизнес процессы, текущее монолитное приложение, текую структуру серверов и базы данных. 
Это требует грамотного анализа, который также будет стоить денег

В целом нужно понимать, что преход на микросервисную архитектуру, это большой и трудоемкий процесс, который потребует финансовых, технических и временных затрат