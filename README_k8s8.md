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
1. Deployment приложения создан

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: lugnginx
  name: lugnginx
spec:
  selector:
    matchLabels:
      app: lugnginx
  replicas: 1 
  template:
    metadata:
      labels:
        app: lugnginx
    spec:
      containers:
      - name: lugnginx
        image: nginx:1.14.2
        volumeMounts:
        - name: foo2
          mountPath: /usr/share/nginx/html/
        ports:
        - containerPort: 80        
      volumes:      
      - name: foo2
        configMap:
          name: nginx-configmap2
```

2. ConfigMap с веб-страницей создан

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-configmap2
  namespace: default
data:
  index.html: |
    <!DOCTYPE html>
    <html lang="ru">
    <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Заголовок страницы</title>
    <link rel="stylesheet" href="./styles/style.css">

    <meta property="og:title" content="Заголовок страницы в OG">
    <meta property="og:description" content="Описание страницы в OG">
    <meta property="og:image" content="https://example.com/image.jpg">
    <meta property="og:url" content="https://example.com/">
    </head>
    <body>
    <header>
      <h1>Это мой сайт</h1>
      <p>Он хороший</p>
      <nav>
        <ul>
          <li><a href="index.html">Эта страница</a></li>
          <li><a href="catalog.html">Другая страница</a></li>
        </ul>
      </nav>
    </header>
    <main>
      <article>
        <section>
          <h2>Первая секция</h2>
          <p>Она обо мне</p>
          <img src="images/image.png" alt="Человек и пароход">
          <p>Но может быть и о семантике, я пока не решил.</p>
        </section>
        <section>
          <h2>Вторая секция</h2>
          <p>Она тоже обо мне</p>
        </section>
        <section>
          <h2>И третья</h2>
          <p>Вы уже должны были начать догадываться.</p>
        </section>
      </article>
    </main>
    <footer>
      <p>Сюда бы я вписал информацию об авторе и ссылки на другие сайты</p>
    </footer>
    <!-- сюда можно подключить jquery <script src="scripts/app.js" defer></script> -->
    </body>
    </html>
```

3. Secret и SSL

Для установки SSL воспользовался следующим сервисом для k8s https://cert-manager.io/

Создал Issuer

```
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
  name: selfsigned-issuer
spec:
  selfSigned: {}
```

Создал сертификат

```
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: my-selfsigned-ca
spec:
  isCA: true
  commonName: my-selfsigned-ca
  secretName: root-secret
  privateKey:
    algorithm: ECDSA
    size: 256
  issuerRef:
    name: selfsigned-issuer
    kind: ClusterIssuer
    group: cert-manager.io
```

Secret создан

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/c72283b6-8587-4f6f-91ab-ddf58a274d69)


4. Ingress и Service создан. SSl подключен

```
apiVersion: v1
kind: Service
metadata:
  name: servnginx
spec:
  selector:
    app: lugnginx
  ports:
    - name: lugnginx
      port: 80
      targetPort: 80
```
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: applug
spec:
  tls:
  - hosts:
    - "my-selfsigned-ca"
    secretName: root-secret
  rules:
  - host: "my-selfsigned-ca"
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: servnginx
            port: 
              number: 80
```

5. Доступ по https

![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/ab22273e-30b7-46f9-8a94-f170c2a1a3f8)


![image](https://github.com/LugovskoyPavel/devops-netology-2022/assets/104651372/c55fbafd-465e-47f6-874c-3b24b66a2470)


------
