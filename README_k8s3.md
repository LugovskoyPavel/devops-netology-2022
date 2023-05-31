# Домашнее задание к занятию «Запуск приложений в K8S»

### Задание 1. Создать Deployment и обеспечить доступ к репликам приложения из другого Pod

1. Создать Deployment приложения, состоящего из двух контейнеров — nginx и multitool. Решить возникшую ошибку.
2. После запуска увеличить количество реплик работающего приложения до 2.
3. Продемонстрировать количество подов до и после масштабирования.
4. Создать Service, который обеспечит доступ до реплик приложений из п.1.
5. Создать отдельный Pod с приложением multitool и убедиться с помощью `curl`, что из пода есть доступ до приложений из п.1.

Ответ:
1. Создал deployment из двух контейнеров
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 1 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 8080
      containers:
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
2. Изменил количество pod. Их сначала было два, потом стал один
```
PS C:\Users\lugy1\.kube> kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-7776f4c7f4-2gjsx   1/1     Running   0          2m27s
nginx-deployment-7776f4c7f4-s9rnc   1/1     Running   0          2m27s
```
3.
```
PS C:\Users\lugy1\.kube> kubectl apply -f dep_ng.yml
deployment.apps/nginx-deployment configured
PS C:\Users\lugy1\.kube> kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-7776f4c7f4-2gjsx   1/1     Running   0          5m43s
```
4. Service создан
```
apiVersion: v1
kind: Service
metadata:
  name: net-ng
spec:
  selector:
    app.kubernetes.io/name: nginx-deployment
  ports:
    - protocol: TCP
      port: 8886
      targetPort: 8080
```

5. Создан отдельный pod multitool
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: multitool-deployment
spec:
  selector:
    matchLabels:
      app: multitool
  replicas: 1 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: multitool
    spec:
      containers:
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
Подключаемся с curl к pods из пункта 1

```
PS C:\Users\lugy1\.kube> kubectl exec -i --tty multitool-deployment-59b888cc67-m8xq5 -- sh            
/ # curl http://10.1.254.76:1180
Praqma Network MultiTool (with NGINX) - nginx-deployment-7776f4c7f4-cr2h2 - 10.1.254.76 - HTTP: 1180 , HTTPS: 11443
<br>
<hr>
<br>

<h1>05 Jan 2022 - Press-release: `Praqma/Network-Multitool` is now `wbitt/Network-Multitool`</h1>     

<h2>Important note about name/org change:</h2>
<p>
Few years ago, I created this tool with Henrik Høegh, as `praqma/network-multitool`. Praqma was bought by another company, and now the "Praqma" brand is being dismantled. This means the network-multitool's git and docker repositories must go. Since, I was the one maintaining the docker image for all these years, it was decided by the current representatives of the company to hand it over to me so I can continue maintaining it. So, apart from a small change in the repository name, nothing has changed.<br> 
</p>
<p>
The existing/old/previous container image `praqma/network-multitool` will continue to work and will remain available for **"some time"** - may be for a couple of months - not sure though.
</p>
<p>
- Kamran Azeem <kamranazeem@gmail.com> <a href=https://github.com/KamranAzeem>https://github.com/KamranAzeem</a>
</p>

<h2>Some important URLs:</h2>

<ul>
  <li>The new official github repository for this tool is: <a href=https://github.com/wbitt/Network-MultiTool>https://github.com/wbitt/Network-MultiTool</a></li>

  <li>The docker repository to pull this image is now: <a href=https://hub.docker.com/r/wbitt/network-multitool>https://hub.docker.com/r/wbitt/network-multitool</a></li>
</ul>

<br>
Or:
<br>

<pre>
  <code>
  docker pull wbitt/network-multitool
  </code>
</pre>

<hr>
```
------

### Задание 2. Создать Deployment и обеспечить старт основного контейнера при выполнении условий

1. Создать Deployment приложения nginx и обеспечить старт контейнера только после того, как будет запущен сервис этого приложения.
2. Убедиться, что nginx не стартует. В качестве Init-контейнера взять busybox.
3. Создать и запустить Service. Убедиться, что Init запустился.
4. Продемонстрировать состояние пода до и после запуска сервиса.

Ответ:


------

