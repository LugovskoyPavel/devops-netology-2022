# Домашнее задание к занятию «Kubernetes. Причины появления. Команда kubectl»

### Цель задания

Для экспериментов и валидации ваших решений вам нужно подготовить тестовую среду для работы с Kubernetes. Оптимальное решение — развернуть на рабочей машине или на отдельной виртуальной машине MicroK8S.

------

### Задание 1. Установка MicroK8S

1. Установить MicroK8S на локальную машину или на удалённую виртуальную машину.
2. Установить dashboard.
3. Сгенерировать сертификат для подключения к внешнему ip-адресу.

Ответ: MicroK8 установлен на машину под управлением Windows. Установка прошла без сбоев. dashboard сгенерирован.

![k8s1](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/64808407-662a-4493-883d-d44d7296e88d)

![k8s2](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/9380a1d1-1d4b-402f-a3f2-15091b4b09cd)

------

### Задание 2. Установка и настройка локального kubectl
1. Установить на локальную машину kubectl.
2. Настроить локально подключение к кластеру.
3. Подключиться к дашборду с помощью port-forward.

Ответ: kubectl установлен. Пришлось немного помучиться, потому что конфигурационный файл был настороен на старые подключения к k8s. пришлось переписать.

Dashboard создан и подключение произведено

Последовательность команд:

1. kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml

2. kubectl proxy

3. kubectl port-forward -n kubernetes-dashboard service/kubernetes-dashboard 8080:443

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/6f53d345-f88f-4b43-bd81-129f6dea74a5)
