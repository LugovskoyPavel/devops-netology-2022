Домашнее задание к занятию 14 «Средство визуализации Grafana»

Обязательные задания
Задание 1
Используя директорию help внутри этого домашнего задания, запустите связку prometheus-grafana.
Зайдите в веб-интерфейс grafana, используя авторизационные данные, указанные в манифесте docker-compose.
Подключите поднятый вами prometheus, как источник данных.
Решение домашнего задания — скриншот веб-интерфейса grafana со списком подключенных Datasource.

Ответ: 
 ![prom](https://user-images.githubusercontent.com/104651372/227761524-79659c73-edd1-4d84-87a6-e26b40ddaab8.png)

Задание 2
Изучите самостоятельно ресурсы:

PromQL tutorial for beginners and humans.
Understanding Machine CPU usage.
Introduction to PromQL, the Prometheus query language.
Создайте Dashboard и в ней создайте Panels:

утилизация CPU для nodeexporter (в процентах, 100-idle);
CPULA 1/5/15;
количество свободной оперативной памяти;
количество места на файловой системе.
Для решения этого задания приведите promql-запросы для выдачи этих метрик, а также скриншот получившейся Dashboard.

Ответ: 
1. 100 * (rate(node_cpu_seconds_total{mode="system"}[1m]))
2. node_load1, node_load5, node_load15
3. node_memory_MemFree_bytes
4. node_filesystem_avail_bytes/(1024*1024*1024)

![monitoring](https://user-images.githubusercontent.com/104651372/227763984-a8684261-ed34-4aad-8fe6-713adce6dcfa.png)

Задание 3
Создайте для каждой Dashboard подходящее правило alert — можно обратиться к первой лекции в блоке «Мониторинг».
В качестве решения задания приведите скриншот вашей итоговой Dashboard.

Ответ: 
![alert](https://user-images.githubusercontent.com/104651372/227764770-60a9d9e0-3739-4e26-85f0-da8e7137465e.png)

Задание 4
Сохраните ваш Dashboard.Для этого перейдите в настройки Dashboard, выберите в боковом меню «JSON MODEL». Далее скопируйте отображаемое json-содержимое в отдельный файл и сохраните его.
В качестве решения задания приведите листинг этого файла.

Ответ: 
[Dashboard.txt](https://github.com/LugovskoyPavel/devops-netology-2022/files/11071124/Dashboard.txt)

