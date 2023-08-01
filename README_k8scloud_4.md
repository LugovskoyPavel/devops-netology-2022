# Домашнее задание к занятию «Обновление приложений»

### Задание 1. Выбрать стратегию обновления приложения и описать ваш выбор

1. Имеется приложение, состоящее из нескольких реплик, которое требуется обновить.
2. Ресурсы, выделенные для приложения, ограничены, и нет возможности их увеличить.
3. Запас по ресурсам в менее загруженный момент времени составляет 20%.
4. Обновление мажорное, новые версии приложения не умеют работать со старыми.
5. Вам нужно объяснить свой выбор стратегии обновления приложения.

### Ответ:

1. В качестве стратегии обновления можно предложить два варианта

Recreate - это создаст простой приложения, но в задании не укказано, что простой не возможен. Эта стратегия позволит нам избежать увеличения нагрузки на ресурсы. А также избежать проблемы несовместимости версий, что указано в задании.
Эта стратегия обновление будет крайне актуально если у нас есть ограничения по работе с БД между старой и новой версией. Так что вполне можно предположить использование этой стратегии. 

Rolling update - возможно использовать при грамотном определении:
- maxUnavailable: как много подов из replicas могут быть уничтожены для запуска новых.
- maxSurge: как много подов может быть создано сверх значения, заданного в replicas.
Эти переменные могут быть заданы явно в количестве или в процентах.
Я бы предложилдля наших условий  0 в maxUnavailable, т.е. не останаливать запущенные поды, пока не будут созданы новые, и maxSurge указали в 1, т.е. во время обновления должен быть создан один дополнительный новый под,
и только после того, как он перейдёт в статус Running – будет остановлен один из старых подов.

Blue-green deployment, canary release - по моим пониманиям условия задания мы не можем позволить себе данные стратегии, так как у нас нет возможности увеличения ресурсов, а запаса в 20 % явно не позволят нам дополнительно развернуть второй depoyment.

### Задание 2. Обновить приложение

1. Создать deployment приложения с контейнерами nginx и multitool. Версию nginx взять 1.19. Количество реплик — 5.
2. Обновить версию nginx в приложении до версии 1.20, сократив время обновления до минимума. Приложение должно быть доступно.
3. Попытаться обновить nginx до версии 1.28, приложение должно оставаться доступным.
4. Откатиться после неудачного обновления.

### Ответ:

1. deployment создан

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx-deployment2
  name: nginx-deployment2
spec:
  selector:
    matchLabels:
      app: nginx-deployment2
  replicas: 5 
  strategy:
    type: RollingUpdate
    rollingUpdate:
       maxSurge: 25%
       maxUnavailable: 25% 
  template:
    metadata:
      labels:
        app: nginx-deployment2
    spec:
      containers:
      - name: nginx
        image: nginx:1.19
        ports:
        - containerPort: 80
      
      - name: network-multitool
        image: praqma/network-multitool
        env:
        - name: HTTP_PORT
          value: "1180"
        - name: HTTPS_PORT
          value: "11443"
        ports:
        - containerPort: 1180
          name: http-port
        - containerPort: 11443
          name: https-port
        resources:
          requests:
            cpu: "1m"
            memory: "20Mi"
          limits:
            cpu: "10m"
            memory: "20Mi"
        securityContext:
          runAsUser: 0
          capabilities:
            add: ["NET_ADMIN"]
```
2. Версия nginx обновлена

Было
![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/4dc54b3c-f3fe-47e5-9c45-6c3a55e31fb4)

Стало
![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/f3a4ed83-fa97-4d79-aa32-d30e07a87392)


3. Попытка обновления до версии 1.28

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/d36cac2d-36b5-4d80-8cb7-ef4529bdf780)

Ошибка обновления на контейнере с nginx

4. Откат после обновления

```
PS C:\Users\lugy1\.kube> kubectl rollout undo deployment nginx-deployment2  
deployment.apps/nginx-deployment2 rolled back
```

