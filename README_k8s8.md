# Домашнее задание к занятию «Конфигурация приложений»

------


### Задание 1. Создать Deployment приложения и решить возникшую проблему с помощью ConfigMap. Добавить веб-страницу

1. Создать Deployment приложения, состоящего из контейнеров busybox и multitool.
2. Решить возникшую проблему с помощью ConfigMap.
3. Продемонстрировать, что pod стартовал и оба конейнера работают.
4. Сделать простую веб-страницу и подключить её к Nginx с помощью ConfigMap. Подключить Service и показать вывод curl или в браузере.
5. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

------
Ответ:
1. Deployment приложения создан

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: busmult
  name: busmult
spec:
  selector:
    matchLabels:
      app: busmult
  replicas: 1 
  template:
    metadata:
      labels:
        app: busmult
    spec:
      containers:
      - name: multlug
        image: praqma/network-multitool
        volumeMounts:
        - name: foo
          mountPath: /usr/share/nginx/html/
        env:
        - name: HTTP_PORT
          value: "80"
        - name: HTTPS_PORT
          value: "11443"
        ports:
        - containerPort: 80 
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
      
      - name: busylug
        image: busybox
        command: ['sh', '-c', 'while true; do echo Success! >> /output/success.txt; sleep 5; done;']
        volumeMounts:
        - name: vol
          mountPath: /output
      volumes:
      - name: vol
        hostPath:
          path: /Data
      - name: foo
        configMap:
          name: nginx-configmap
```

2. ConfigMap создан

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-configmap
  namespace: default
data:
  index.html: |
    <html>
    <h1>Welcome</h1>
    </br>
    <h1>Hi! This is a Netology </h1>
    </html
```

3. Pod с контейнерами работают исрпвно, скрин из Lens

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/9f2f1eed-523a-49f6-8491-4cfd985c7b56)

4. Service создан

```
apiVersion: v1
kind: Service
metadata:
  name: servconf
spec:
  selector:
    app: busmult
  ports:
    - name: nginxconf
      port: 80
      targetPort: 80
```

5. Скриншот страницы в браузере

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/eb177a85-7807-49f9-baf8-1fa538f1b9c1)


------

### Задание 2. Создать приложение с вашей веб-страницей, доступной по HTTPS 

1. Создать Deployment приложения, состоящего из Nginx.
2. Создать собственную веб-страницу и подключить её как ConfigMap к приложению.
3. Выпустить самоподписной сертификат SSL. Создать Secret для использования сертификата.
4. Создать Ingress и необходимый Service, подключить к нему SSL в вид. Продемонстировать доступ к приложению по HTTPS. 
4. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

------
Ответ:

------
