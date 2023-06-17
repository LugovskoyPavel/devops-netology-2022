# Домашнее задание к занятию «Сетевое взаимодействие в K8S. Часть 1»

------

### Задание 1. Создать Deployment и обеспечить доступ к контейнерам приложения по разным портам из другого Pod внутри кластера

1. Создать Deployment приложения, состоящего из двух контейнеров (nginx и multitool), с количеством реплик 3 шт.
2. Создать Service, который обеспечит доступ внутри кластера до контейнеров приложения из п.1 по порту 9001 — nginx 80, по 9002 — multitool 8080.
3. Создать отдельный Pod с приложением multitool и убедиться с помощью `curl`, что из пода есть доступ до приложения из п.1 по разным портам в разные контейнеры.
4. Продемонстрировать доступ с помощью `curl` по доменному имени сервиса.
5. Предоставить манифесты Deployment и Service в решении, а также скриншоты или вывод команды п.4.

Ответ:

1. Deployment создан
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
  replicas: 3 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: nginx-deployment2
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
2. Services создан

```
apiVersion: v1
kind: Service
metadata:
  labels:
    app: my-nginx2
  name: my-nginx2
spec:
  selector:
    app: nginx-deployment2
  ports:
    - name: myng2
      protocol: TCP 
      port: 9001
      targetPort: 8080
    - name: mymult2
      protocol: TCP
      port: 9002
      targetPort: 1180
```
3. Создать отдельный Pod с приложением multitool
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
4. Доступ обеспечен

```
PS C:\Users\lugy1\.kube> kubectl exec --stdin --tty multitool-deployment-6b94bcf447-g9j66 -- /bin/bash
bash-5.1# curl my-nginx2:9002
Praqma Network MultiTool (with NGINX) - nginx-deployment2-7fc55ccb4d-wjfnq - 10.1.254.98 - HTTP: 1180 , HTTPS: 11443
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

5. Манифесты и вывод команд предоставлены


------

### Задание 2. Создать Service и обеспечить доступ к приложениям снаружи кластера

1. Создать отдельный Service приложения из Задания 1 с возможностью доступа снаружи кластера к nginx, используя тип NodePort.
2. Продемонстрировать доступ с помощью браузера или `curl` с локального компьютера.
3. Предоставить манифест и Service в решении, а также скриншоты или вывод команды п.2.

Ответ:

1. Сервис создан
```
apiVersion: v1
kind: Service
metadata:
  labels:
    app: my-nginx3
  name: my-nginx3
spec:
  selector:
    app: nginx-deployment2
  ports:
    - name: ng-web
      port: 1180
      protocol: TCP 
      nodePort: 30080
  type: NodePort
```

2. Доступ из браузера к сервису
```
bash-5.1# curl 10.152.183.239:1180
Praqma Network MultiTool (with NGINX) - nginx-deployment2-7fc55ccb4d-wjfnq - 10.1.254.98 - HTTP: 1180 , HTTPS: 11443
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


3. Манифесты и вывод команд предоставлены

------

